实现一行三个平均分，间距相等，上下两行间距也想等

```html
<div class="box">
	<div class="item">column1</div>
	<div class="item">column2</div>
	<div class="item">column3</div>
	<div class="item">column4</div>
	<div class="item">column5</div>
	<div class="item">column6</div>
</div>
```

```css
<style type="text/css">
		.box {
			width: 100%;
			height: 400px;
			display: grid;
			grid-template-columns: 1fr 1fr 1fr;
			column-gap: 10px;
			row-gap: 10px;
			gap: 10px;
		}
		.item {
			background-color: blue;
		}
	</style>
```

