# Echarts

## 在uniapp中使用Echart

### 第一步: 安装依赖

```
npm install echarts mpvue-echarts --save
```

### 第二部：下载echarts.min.js

在线自定义echarts.min.js：

[https://echarts.apache.org/zh/builder.html]: 

### 第三步：在components文件夹中新建一个公共组件echarts.vue，

>注意：引入echarts库的路径 script.src = ‘./static/echarts.min.js

```vue
<template>
	<view>
		<view class="echarts" :prop="option" :change:prop="echarts.update"></view>
	</view>
</template>
 
<script>
	export default {
		name: 'Echarts',
		props: {
			option: {
				type: Object,
				required: true
			}
		}
	}
</script>
 
<script module="echarts" lang="renderjs">
	export default {
		data() {
			return {
				chart: null
			}
		},
		mounted() {
			if (typeof window.echarts === 'object') {
				this.init()
			} else {
				// 动态引入类库
				const script = document.createElement('script')
				// 在这里修改引用echarts.min.js的路径
				script.src = './static/echarts.min.js'
				// script.src = './static/echarts/echarts.min.js'
				script.onload = this.init
				document.head.appendChild(script)
			}
		},
		methods: {
			/**
			 * 初始化echarts
			 */
			init() {
				this.chart = echarts.init(this.$el)
				this.update(this.option)
			},
			/**
			 * 监测数据更新
			 * @param {Object} option
			 */
			update(option) {
				if (this.chart) {
					// 因App端，回调函数无法从renderjs外传递，故在此自定义设置相关回调函数
					if (option) {
						// tooltip
						if (option.tooltip) {
							// 判断是否设置tooltip的位置
							if (option.tooltip.positionStatus) {
								option.tooltip.position = this.tooltipPosition()
							}
							// 判断是否格式化tooltip
							if (option.tooltip.formatterStatus) {
								option.tooltip.formatter = this.tooltipFormatter(option.tooltip.formatterUnit, option.tooltip.formatFloat2, option.tooltip.formatThousands)
							}
						}
                    // 设置新的option
					this.chart.setOption(option, option.notMerge)
					}
					
				}
			},
			/**
			 * 设置tooltip的位置，防止超出画布
			 */
			tooltipPosition() {
				return (point, params, dom, rect, size) => {
					//其中point为当前鼠标的位置，size中有两个属性：viewSize和contentSize，分别为外层div和tooltip提示框的大小
					let x = point[0]
					let y = point[1]
					let viewWidth = size.viewSize[0]
					let viewHeight = size.viewSize[1]
					let boxWidth = size.contentSize[0]
					let boxHeight = size.contentSize[1]
					let posX = 0 //x坐标位置
					let posY = 0 //y坐标位置
					if (x < boxWidth) { //左边放不开
						posX = 5
					} else { //左边放的下
						posX = x - boxWidth
					}
					if (y < boxHeight) { //上边放不开
						posY = 5
					} else { //上边放得下
						posY = y - boxHeight
					}
					return [posX, posY]
				}
			},
			/**
			 * tooltip格式化
			 * @param {Object} unit 数值后的单位
			 * @param {Object} formatFloat2 是否保留两位小数
			 * @param {Object} formatThousands 是否添加千分位
			 */
			tooltipFormatter(unit, formatFloat2, formatThousands) {
				return params => {
					let result = ''
					unit = unit ? unit : ''
					for (let i in params) {
						if (i == 0) {
							result += params[i].axisValueLabel
						}
						let value = '--'
						if (params[i].data !== null) {
							value = params[i].data
							// 保留两位小数
							if (formatFloat2) {
								value = this.formatFloat2(value)
							}
							// 添加千分位
							if (formatThousands) {
								value = this.formatThousands(value)
							}
						}
						// #ifdef H5
						result += '\n' + params[i].seriesName + '：' + value + ' ' + unit
						// #endif
						
						// #ifdef APP-PLUS
						result += '<br/>' + params[i].marker + params[i].seriesName + '：' + value + ' ' + unit
						// #endif
					}
					return result
				}
			},
			/**
			 * 保留两位小数
			 * @param {Object} value
			 */
			formatFloat2(value) {
				let temp = Math.round(parseFloat(value) * 100) / 100
				let xsd = temp.toString().split('.')
				if (xsd.length === 1) {
					temp = (isNaN(temp) ? '0' : temp.toString()) + '.00'
					return temp
				}
				if (xsd.length > 1) {
					if (xsd[1].length < 2) {
						temp = temp.toString() + '0'
					}
					return temp
				}
			},
			/**
			 * 添加千分位
			 * @param {Object} value
			 */
			formatThousands(value) {
				if (value === undefined || value === null) {
					value = ''
				}
				if (!isNaN(value)) {
					value = value + ''
				}
				let re = /\d{1,3}(?=(\d{3})+$)/g
				let n1 = value.replace(/^(\d+)((\.\d+)?)$/, function(s, s1, s2) {
					return s1.replace(re, '$&,') + s2
				})
				return n1
			}
		}
	}
</script>
 
<style lang="scss" scoped>
	.echarts {
		width: 100%;
		height: 100%;
	}
</style>

```

### 第四步：在page目录下新建一个页面，也就是需要引入图表的页面。

```vue
		<!-- 指标图表展示 -->
		<view class="graph">
			<view class="oil-amount index-item">
				<text>加油量(升)</text>
				<view class="index-detail">
					<echarts :option="option" style="height: 500rpx;"></echarts>
				</view>
			</view>
		</view>


<script>
	import echarts from '@/components/echarts/echarts.vue';
	export default {

		data() {
			return {
				option: {},
				myChart: null,
				keyIndex: {
					// 柱状图数据
					"refuelIntList": [
						476.49,
						0
					], //加油量统计表数值
					"refuelTimeList": [
						"2022-08-03",
						"2022-08-04"
					], //加油量统计表时间
					"oilWearIntList": [
						79.48,
						103.76
					], //总油耗统计表数值
					"oilWearTimeList": [
						"2022-08-03",
						"2022-08-04"
					], //总油耗统计表时间
					"oilLeakIntList": [
						581.53,
						0
					], //漏油量统计表数值
					"oilLeakTimeList": [
						"2022-08-03",
						"2022-08-04"
					] //漏油量统计表时间
				},

			}
		},

		methods: {
			logstatrt() {
				console.log('初次调用');
				this.option = {
					// 悬浮、点击出现相关信息，有阴影
					tooltip: {
						trigger: 'axis',
						triggerOn: 'click',
						axisPointer: {
							type: 'shadow'
						},
						formatter: function(params) {
							var tar = params[1];
							return tar.name + '<br/>' + tar.seriesName + ' : ' + tar.value;
						}
					},
					xAxis: {
						// x轴坐标数据
						data: this.keyIndex.refuelTimeList,
					},
					yAxis: {
						y轴数据类型
						type: 'value'
					},
					// 相关样式
					series: [{
							// y轴数据，echart会根据数据自动展示y轴坐标
							data: this.keyIndex.refuelIntList,
							// 柱状图
							type: 'bar',
							backgroundStyle: {
								color: 'rgba(46, 128, 240)',
							},
							// 在柱状图中中间显示数据
							label: {
								show: true,
								position: 'inside'
							},
						},
					],
				}
			},
		mounted() {
			this.logstatrt();
		},

		components: {
			echarts
		},
	}
</script>
```

