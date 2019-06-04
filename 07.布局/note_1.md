## 布局

### No.1 垂直水平居中问题

html 部分
```
<div id="box"></div>
```

css 部分
```
/* 方式一：使用绝对定位 + margin */
#box {
	position: absolute;
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	margin: auto;
}
/* 方式二：使用绝对定位 + translate */
#box {
	position: absolute;
	top: 50 %;
	left: 50 %;
	transform: translate(-50%, -50%);
}
/* 方式三：使用 flex */
#box {
	display: flex;
	justify-content: center;
	align-items: center;
}
/* 方式四：使用 table-cell */
#box {
	display: table-cell;
	vertical-align: middle;
}
```

### No.2 实现一个三栏布局，左右固定，中间自适应

**定义 CSS 样式类**
```
.bg-red {background-color: red;}
.bg-green {background-color: green;}
.bg-blue {background-color: blue;}
.w-200 {width: 200px;}
.h-200 {height: 200px;}
```

#### Flex 三栏布局
html 部分
```
<div class="layout_1">
	<div class="center bg-green h-200"></div>
	<div class="left bg-red h-200"></div>
	<div class="right bg-blue h-200"></div>
</div>
```

css 部分
```
// 左右固定，中间弹性伸缩
.layout_1 {
	display: flex;
	flex-direction: row;
}
.layout_1 .center {
	flex-grow: 1;
}
.layout_1 .left {
	order: -1;
	flex: 0 1 200px;
}
.layout_1 .right {
	flex: 0 1 200px;
}
```

#### 浮动布局
html 部分
```
<div class="layout_2">
	<div class="left bg-red h-200 w-200"></div>
	<div class="right bg-blue h-200 w-200"></div>
	<div class="center bg-green h-200"></div>
</div>
```

css 部分
```
// 左右浮动，中间使用 margin 或开启 BFC
.layout_2 .left { float: left; }
.layout_2 .right { float: right; }
.layout_2 .center {
    /* margin: 0 220px; */ /* 左右 margin 的方式 */
    overflow: hidden; /* 开启 BFC */
}
```

#### 圣杯布局
html 部分
```
<div class="layout_3">
    <div class="center bg-green h-200"></div>
    <div class="left bg-red h-200 w-200"></div>
    <div class="right bg-blue h-200 w-200"></div>
</div>
```

css 部分
```
// 统一浮动，左右两侧使用负外边距调整位置
.layout_3 {
    min-width: 200px; /* 设置最小宽度，以防布局错乱 */
    padding: 0 200px;
    overflow: hidden;
}
.layout_3 .center {
    float: left;
    width: 100%;
}
.layout_3 .left {
    float: left;
    margin-left: -100%;
    position: relative;
    right: 200px;
}
.layout_3 .right {
    float: left;
    margin-right: -200px; /* 负外边距将影响元素边界的收缩 */
}
```

#### 双飞翼布局
html 部分
```
<div class="layout_4">
    <div class="wrapper">
        <div class="center bg-green h-200"></div>
    </div>
    <div class="left bg-red h-200 w-200"></div>
    <div class="right bg-blue h-200 w-200"></div>
</div>
```

css 部分
```
// 统一浮动，中间添加包裹层
.layout_4 {
    overflow: hidden;
}
.layout_4 .wrapper {
    float: left;
    width: 100%;
}
.layout_4 .wrapper .center {
    margin: 0 200px;
}
.layout_4 .left {
    float: left;
    margin-left: -100%;
}
.layout_4 .right {
    float: left;
    margin-left: -200px;
}
```

#### absolute 三栏布局
html 部分
```
<div class="layout_5">
    <div class="center bg-green"></div>
    <div class="left bg-red h-200 w-200"></div>
    <div class="right bg-blue h-200 w-200"></div>
</div>
```

css 部分
```
// 中间绝对定位，并预留出两侧的空间
.layout_5 {
    position: relative;
    overflow: hidden; /* 防止高度塌陷 */
}
.layout_5 .center {
    position: absolute;
    left: 200px;
    right: 200px;
    /* 使用 top 和 bottom 撑开高度 */
    top: 0;
    bottom: 0;
}
.layout_5 .left { float: left; }
.layout_5 .right { float: right; }
```

### No.3 rem 适配 - 修改根标签字体大小，以实现屏幕的等比分割
```
function remAdapter() {
	var styleNode = document.createElement('style');
	styleNode.innerHTML = 'html{font-size: ' + document.documentElement.clientWidth / 16 + 'px !important;}'
	document.head.appendChild(styleNode);
}
```

### No.4 viewport 适配 - 修改页面的初始缩放值，以适配设计图尺寸，视觉效果
```
function vpAdapter() {
    var width = document.documentElement.clientWidth; // 获取屏幕宽度
    var targetW = 320; // 设计图宽度
    var scale = width / targetW; // 比例
    var metaNode = document.querySelector('meta[name="viewport"]'); // 获取 meta 标签
    metaNode.setAttribute('content', 'initial-scale=' + scale); // 设置缩放比
}
```

### No.5 1物理像素
```
// 布局元素采用 rem 单位，对于边框 1px 采用像素单位，统一把所有元素都缩小一半，布局元素把削减的比例乘回来
! function() {
	var width = document.documentElement.clientWidth;
	var dpr = window.devicePixelRatio; // 获取设备像素比
	var scale = 1 / dpr; // 比例
	var metaNode = document.querySelector('meta[name="viewport"]')

	// 元素比例乘回来
	var htmlNode = document.querySelector('html');
	htmlNode.style.fontSize = width / 16 * dpr + 'px';
}();
// 另,还有使用 transform: scaleX() scaleY() 等方式...
```

### No.6 pc 浏览器兼容性适配 - Hack 技术
- a. 通过内核前缀直接使用该浏览器试验阶段的属性样式，
- b. 针对 IE 浏览器可使用其独有的条件表达式
- c. 使用 CSS3 PIE 插件，以兼容低版本 IE 浏览器的圆角、阴影等样式
- d. html5shiv 处理新增标签兼容、prefixfree 解决书写前缀的麻烦及 Respond 兼容媒体查询的功能