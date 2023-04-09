# uni-app

### 1. 组件基本使用

#### text

1. selectable：是否可选

#### view（就是div，写成div会自动转换成view）

1. hover-calss：按下去的样式
2. hover-stop-propagation：阻止冒泡
3. hover-start-time：点击后延迟多久生效
4. hover-stay-time：点击结束后多久后消失

#### button

1. size
2. type
3. plain：是否镂空，背景色透明
4. disabled：禁用
5. loading：正在加载
6. password：密码框

#### image（放图片）

### 2. uniapp样式以及如何在uniapp中使用scss

#### rpx

1. 根据设备宽度自适应改变元素大小

#### 使用图标

1. 将字体图标静态资源引入进来
2. 全局使用可以在全局中引入iconfont.css
3. 将静态资源的中引入路径改成`~@/static/fonts/....`形式
4. 在组件中使用`<view class="iconfont icon-tupian">...</view>`

### 3. 生命周期函数的使用

#### 实现下拉刷新的两种方式

1. 在pages.json中对应页面的style对象中添加`enablePullDownRefresh:true;`

2. 通过定义点击事件触发`uni.startPullDownRefresh()`函数，前提是要先将方式一打开

#### 停止下拉刷新

1. `onPullDownRefresh`函数可以监听下拉刷新事件
2. `uni.stopPullDownRefresh()`方法可以停止下拉刷新

#### 页面触底

1. `onReachBottom`函数监听页面触底事件
2. 在`pages.json`可以通过修改`onReachBottomDistance`属性改变触发触底的距离

#### 发送请求

1. 调用`uni.request({})`函数，参数是一个对象，在对象里面添加配置
2. `success(res) {}`回调函数接收服务器返回的数据

#### 数据缓存

1. 同步缓存
   1. `uni.setStorageSync('id',100)`：存数据
   2. `const res = uni.getStorageSync('id')`：取数据
   3. `uni.removeStorageSync('id')`：清除数据
2. 异步缓存
   1. `uni.setStorage({})`：存数据
   2. `uni.getStorage({}})`：取数据
   3. `uni.removeStorage({})`: 清除数据

#### 上传图片

1. 上传图片
   1. uni.chooseImage({}):  设置事件触发Api上传图片（参数：count限制上传图片张数；success上传成功返回的回调函数，可以用于将上传的图片展示出来）
2. 预览图片
   1. uni.previewImage({}): 预览图片Api（参数： current当前显示图片的链接、索引值；urls：需要预览的图片链接列表）

#### 条件编译跨端兼容

```
		<!-- #ifdef H5 -->
		<view>
			H5中显示
		</view>
		<!-- #endif -->
		<!-- #ifdef MP-WEIXIN -->
		<view>
			微信小程序中显示
		</view>
		<!-- #endif -->
```

在css、js中同理

#### 路由跳转与传参

1. 路由跳转的两种方式

   1. 声明式：`<navigator url="/pages/detail/detail">跳转到详情页</navigator>`

      1. 跳转到tabBar页需要设置`open-type="switchTab"`
      2. `open-type="redirect"`：关闭当前页面前往下一个页面

   2. 编程式:  

      1. `uni.navigateTo({})`: 保留当前页面，跳转到应用内的某个页面，使用`uni.navigateBack`可以返回到原页面。
      2. `uni.redirectTo({})`: 关闭当前页面，跳转到应用内的某个页面。
      3. `uni.switchTab({})`: 跳转到 tabBar 页面，并关闭其他所有非 tabBar 页面。
      4. `uni.navigateBack`: 关闭当前页面，返回上一页面或多级页面。可通过 `getCurrentPages()` 获取当前的页面栈，决定需要返回几层。

   3. 传参

      1. `<navigator url="/pages/detail/detail?id=10">跳转到详情页</navigator>`：路径后面加？+ 参数

      2. 接收参数在相应页面的onLoad生命周期钩子中使用options参数接收

         ```
         onLoad(option) {
         	console.log(option)
         },
         ```

#### 组件的创建与生命周期与vue2一样

#### 组件之间通讯（与vue一样）

1. 父向子（自定义属性）

   1. 父组件自定义属性
   2. 子组件用 props接收

2. 子向父（自定义事件）

   1. 子组件自定义事件

      ```javascript
      <button @click="emitNum">点击给父组件传num</button>
      data() {
      	return {
      		num: 2
      	};
      },
      methods: {
      	emitNum() {
      		this.$emit('testNum',this.num)
      	}
      }
      ```

   2. 父组件根据子组件自定义的事件，在子组件绑定该事件触发另一个事件

      ```javascript
      <Test :title='title' @testNum="getNum"></Test>
      <view>
      	{{num}}
      </view>
      data() {
      	return {
      		title: 'Home',
      		num: 0
      	}
      },
      methods: {
      	getNum(num) {
      		this.num = num
      	}
      
      },
      ```

3. 兄弟之间（`uni.$emit(event,{})`、`uni.$on(event,(res) => {res为接收过来的数据})`）

### 4.uni-ui

#### 使用

1. uni-app官网 => 组件 => 扩展组件
2. 下载安装 => 使用HbuildX导入插件



## app专题

### nvue

####  App 端某些 vue 页面表现不佳的场景下使用 nvue 作为强化补充

1. 需要高性能的区域长列表或瀑布流滚动。webview 的页面级长列表滚动是没有性能问题的（就是滚动条覆盖 webview 整体高度），但页面中某个区域做长列表滚动，则需要使用 nvue 的`list`、`recycle-list`、`waterfall`等组件([详见 (opens new window)](https://uniapp.dcloud.io/component/list))。这些组件的性能要高于 vue 页面里的区域滚动组件`scroll-view`。

## 问题

**在开发安卓应用时，因为安卓模拟器性能较差，uni-app官方不推荐使用，所以选择真机运行的方式调试。**

**上手就踩了一个小坑，在 HBuilderX 内点击 运行——运行到手机或模拟器，提示 “未检测到手机或模拟器”**

#### 解决方法

1. 手机连接电脑；
2. **设置**—**关于手机**，连续点击7次“版本号”，进入开发者模式；
3. 回退到设置界面，**系统和更新**—**开发人员选项**，打开**开发人员选项**、**USB调试**、**“仅充电”模式下运行ADB调试**；
4. 此时HBuilderX会提示检测到手机，重新点击**运行**—**运行到手机或模拟器**—**运行-[设备xxx]**

**注意：如果未生效，断开重连手机和电脑，或者更换数据线重试**



```
			// 向后台发送修改密码请求
			changePwd() {
				if() {}
				this.resetPwd()
			},

			// 修改密码请求
			resetPwd() {
				var that = this;
				this.service.postJson("lead/person/updatePwd", {
					"oldPassword": this.oldPassword,
					"newPassword": this.newPassword
				}, function(data) {
					console.log(data.code)
					if (data.code === 200) {
						plus.nativeUI.toast("修改成功，下次使用新密码登录")
					} else {
						plus.nativeUI.toast(data.msg);
					}
				}, function(err) {
					console.log(err)
				});
			},
```

