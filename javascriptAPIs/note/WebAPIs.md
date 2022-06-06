## 一、WebAPIs简介

JavaScript基础学习的知识绝大部分属于ECMAScript的知识体系。

ECMAScript简称。ES它提供了一套语言标准规范，如变量、数据类型、表达式、语句、函数等语法规则。

浏览器将ECMAScript大部分的规范加以实现，并且在此基础上又扩展一些实用的功能，这些被扩展出来的内容称为 Web APIs。

ECMAScript运行在浏览器中然后再结合 Web APIs 才是真正的JavaScript，Web APIs的核心是DOM和BOM。

ECMAScript 规范在不断的更新中，存在多个不同的版本，早期的版本号采用数字顺序编号如 ECMAScript3、ECMAScript5，后来由于更新速度较快便采用年份做为版本号，如 ECMAScript2017、ECMAScript2018 这种格式，ECMAScript6 是 2015 年发布的，常叫做 EMCAScript2015。

## 二、DOM

DOM（Document Object Model 文档对象模型）是将整个 HTML 文档的每一个标签元素视为一个对象，这个对象下包含了许多的属性和方法，通过操作这些属性或者调用这些方法实现对 HTML 的动态更新，为实现网页特效以及用户交互提供技术支撑。

简言之 **DOM 是用来动态修改HTML的，其目的是开发网页特效及用户交互**。

### 2.1 DOM树

如下图所示，将 HTML 文档以树状结构直观的表现出来，称之为文档树或 DOM 树，**文档树直观的体现了标签与标签之间的关系。**

<img src="https://cdn.jsdelivr.net/gh/yuhuo2022/pic-bed/web/pic202204130152069.jpg" alt="dom" style="zoom:50%;" />

### 2.2 DOM对象（节点）

节点是文档树的组成部分，**每一个节点都是一个 DOM 对象**，主要分为元素节点、属性节点、文本节点等。

DOM对象：浏览器根据html标签生成的JS对象。所有的标签属性都可以再DOM对象上找到，修改DOM对象的属性会自动映射到标签上。

**DOM的核心思想就是把网页内容当做对象来处理。**

1. 【元素节点】其实就是 HTML 标签，如上图中 `head`、`div`、`body` 等都属于元素节点。
2. 【属性节点】是指 HTML 标签中的属性，如上图中 `a` 标签的 `href` 属性、`div` 标签的 `class` 属性。
3. 【文本节点】是指 HTML 标签的文字内容，如 `title` 标签中的文字。
4. 【根节点】特指 `html` 标签。

### 2.3 document

`document` 是 JavaScript内置的专门用于DOM的对象，该对象包含了若干的属性和方法，`document` 是DOM 的核心。

### 2.4 获取DOM对象

`document.querySelector('CSS选择器')`满足条件的第一个元素。参数是一个或多个有效的**CSS选择器字符串**。返回值是CSS选择器匹配的**第一个元素**，是一个HTMLElement对象。

`document.querySelectorAll('CSS选择器')`满足条件的元素集合 返回伪数组（NodeList对象集合）。

伪数组：有长度有索引号，但没有pop、push等数组方法。

想要得到伪数组中的每一个对象，需要遍历。

注意：哪怕只有一个元素，通过document.querySelectorAll获取过来也是一个伪数组。

```html
<body>
<div class="box">123</div>
<div class="box">abc</div>
<p id="nav">导航栏</p>
<ul>
	<li>西红柿炒圣女果</li>
	<li>西红柿炒番茄</li>
	<li>番茄炒西红柿</li>
</ul>
<script>
	// 获取匹配的第一个元素
	const box = document.querySelector('div');
	console.log(box); // => <div class="box">123</div>

	const box1 = document.querySelector('.box');
	console.log(box1); // => <div class="box">123</div>

	const nav = document.querySelector('#nav');
	console.log(nav); // => <p id="nav">导航栏</p>

	const li = document.querySelector('ul li:first-child');
	console.log(li); // => <li>西红柿炒圣女果</li>

	// 获取匹配的所有元素
	const lis = document.querySelectorAll('ul li');
	console.log(lis); // => NodeList(3) [li, li, li]
  for (let i = 0; i < lis.length; i++) {
		console.log(lis[i]);
	}
</script>
</body>
```

其他常用方式

1. `document.getElementById('#id')`
2. `document.getElementsByTagName('标签名')`
3. `document.getElementsByClassName('类名')`

注意：

- document.getElementById 专门获取元素类型节点，根据标签的 `id`  属性查找
- 任意 DOM 对象都包含 nodeType 属性，用来检测节点类型

### 2.5 操纵元素内容

通过修改 DOM 的文本内容，动态改变网页的内容。

`innerText` 将文本内容添加/更新到任意标签位置，**文本中包含的标签不会被解析。**

`innerHTML` 将文本内容添加/更新到任意标签位置，**文本中包含的标签会被解析。**

如果文本内容中包含 `html` 标签时推荐使用 `innerHTML`，否则建议使用 `innerText` 属性。

```html
<div class="box">种下梧桐树，引得凤凰来。面朝大海，春暖花开</div>
<script>
	// 获取元素
	const box = document.querySelector('.box');
	// 获取、修改文字内容  对象.innerText
	console.log(box.innerText);
	box.innerText = '眼看他起高楼，眼看他宴宾客，眼看他楼塌了';
	// innerText不解析标签
	box.innerText = '<h4>这是一个4级标题</h4>';
	
	// 要解析带标签的元素内容，可以使用 元素.innerHTML
	box.innerHTML = '<strong>最是人间留不住，朱颜辞镜花辞树</strong>'
</script>
```

在开发中习惯使用innerHTML，这样更方便。

### 2.6 操作元素属性

有3种方式可以实现对属性的修改，每种方式都有适用的场景。

#### 2.6.1 常用属性操纵

宽、高等常用属性可以直接通过属性名修改，这最简洁的语法。

```html
<img alt="" src="./案例/images/1.webp">
<script>
	const img = document.querySelector('img');
	img.src = './案例/images/2.webp';
	img.title = 'hi';
</script>
```

#### 2.6.2 控制样式属性

1. 应用【修改样式】，通过修改行内样式 `style` 属性，实现对样式的动态修改。

通过元素节点获得的 `style` 属性本身的数据类型也是对象，如 `div.style.color`、`div.style.width` 分别用来获取元素节点 CSS 样式的 `color` 和 `width` 的值。

任何标签都有 `style` 属性，通过 `style` 属性可以动态更改网页标签的样式，如要遇到 `css` 属性中包含字符 `-` 时，要将 `-` 去掉并将其后面的字母改成大写，如 `background-color` 要写成 `div.style.backgroundColor`

```html
<script>
	const div = document.querySelector('div');
	// 修改样式属性 对象.style.样式属性='值' 注意要加单位
	div.style.width = '300px';
	// 多组单词用小驼峰命名
	div.style.backgroundColor = '#F0F';
	div.style.border = '5px solid #000';
</script>
```

2. 操作类名(className) 操作CSS

如果修改的样式比较多，直接通过style属性修改比较繁琐，可以通过借助于css类名的形式。

由于class是关键字, 所以使用className去代替。

className是使用新值换旧值, 如果需要添加一个类,需要保留之前的类名

```html
<style>
		div {
			width: 200px;
			height: 200px;
			background-color: #0FF;
		}

		.nav {
			color: #000;
		}

		.box {
			width: 300px;
			height: 300px;
			margin: 100px auto;
			padding: 10px;
			border: 1px solid #000;
			background-color: #F0F;
		}
	</style>
<div class="nav">12</div>
<script>
	const div = document.querySelector('div');
	// 添加类名，class是关键字，用className代替。这种方式有覆盖问题，要注意
	// div.className = 'box';
	div.className = 'nav box';
</script>
```

3. 通过 classList 操作类控制CSS

**为了解决className容易覆盖以前的类名，可以通过classList方式追加和删除类名**

这种方式是开发中使用较多的。

```html
<style>
		div {
			width: 200px;
			height: 200px;
			background-color: #0FF;
		}

		.box {
			color: #00A4FF;
		}

		.active {
			width: 300px;
			height: 300px;
			background-color: #F0F;
			margin-left: 100px;
			color: #050505;
		}
	</style>
<div class="box">空山新雨后，自挂东南枝</div>
<script>
	let box = document.querySelector('div');
	// add是个方法 添加
	box.classList.add('active');
	// remove() 移除 类
	box.classList.remove('box');
	// 切换类 toggle 有就删除，没有则添加
	box.classList.toggle('box');
</script>
```

#### 2.6.3 操作表单元素属性

表单很多情况，也需要修改属性，比如点击眼睛，可以看到密码，本质是把表单类型转换为文本框

正常的有属性有取值的跟其他的标签属性没有任何区别

获取:DOM对象.属性名

设置:DOM对象.属性名= 新值

表单属性中添加就有效果，移除没有效果，一律用布尔值表示。true表示添加了该属性，false表示移除。这样的属性有：disabled、checked、selected

```html
<label>
	<input type="text" value="今天星期六">
	<input checked id="man" name="gender" type="checkbox">男
	<input id="woman" name="gender" type="checkbox"> 女
	<!-- 	属性和属性值相同可以简写，表示true -->
	<button disabled>点击</button>
</label>
<script>
	const input = document.querySelector('input');
	// 获取表单的值用 表单.value
	console.log(input.value);
	// 设置属性
	input.value = '周六';
	input.type = 'password';

	const man = document.querySelector('#man');
	const woman = document.querySelector('#woman');
	// checked只接受布尔值
	man.checked = false;
	woman.checked = true;

	const button = document.querySelector('button');
	console.log(button.disabled);
	// 默认false，不禁用
	button.disabled = false;
</script>
```

#### 2.6.4 自定义属性

标准属性: 标签天生自带的属性 比如class id title等, 可以直接使用点语法操作比如： disabled、checked、selected

自定义属性：

在html5中推出来了专门的data-自定义属性  

在标签上一律以data-开头

在DOM对象上一律以dataset对象方式获取

```html
<div data-id="1" data-spm="spm">1</div>
<div data-id="2">2</div>
<script>
	const one = document.querySelector('div');
	// 获取只定义属性 对象.dataset
	console.log(one.dataset.id);
	console.log(one.dataset.spm);
</script>
```

## 三、定时器

### 3.1 间歇函数setInterval

`setInterval` 是 JavaScript 中内置的函数，它的作用是间隔固定的时间自动重复执行另一个函数，也叫定时器函数。

开启定时器：`setInterval(函数，间隔时间)`。间隔时间单位是毫秒

定时器返回的是一个定时器id数字，通过这个id可以控制定时器开关。

关闭定时器 clearInterval(定时器id)。

```js
	// 写法1
	setInterval(function () {
		console.log('一秒执行一次');
	}, 1000);

	function fn() {
		console.log('一秒执行一次');
	}

	// 写法二： 注意函数名不能加小括号调用 或者 'fu()'
	let timeId = setInterval(fn, 1000);

	// 关闭定时器 clearInterval(定时器id)
	clearInterval(timeId);
```

### 3.2 延时函数setTimeout

setTimeout是JS内置的一个让代码延迟执行的函数。

语法：`setTimeout(回调函数,延迟时间)`

setTimeout只执行一次，可以理解为把一段代码延迟执行。

清除延时函数：`clearTimeout(延时函数id);`

```js
	// 开启延时函数
	let timer = setTimeout(function () {
		console.log(123);
	}, 1000);
	// 清除延时函数
	clearTimeout(timer);
```

### 3.3 两种定时器区别

两种定时器最大的区别就是执行次数不同。

间歇函数setInterval每隔一段时间就执行一次，除非手动清除。

延时函数setTimeout只执行一次。

## 四、事件

事件是编程语言中的术语，它是用来描述程序的行为或状态的，**一旦行为或状态发生改变，便立即调用一个函数。**

例如：用户使用【鼠标点击】网页中的一个按钮、用户使用【鼠标拖拽】网页中的一张图片

### 4.1 事件监听

事件监听也叫事件绑定。

结合DOM使用事件时，需要为DOM对象添加事件监听，等待事件发生（触发）时，便立即调用一个函数。

`addEventListener('事件类型',事件回调)`是DOM对象专门用来添加事件监听的方法，它的两个参数分别为【事件类型】和【事件回调】。

**注意：事件类型要加引号，是字符串，事件函数不会立即执行，被触发才会执行，触发一次执行一次。**

事件监听三要素：

- 事件源：那个DOM元素被事件触发了，要获取DOM元素。
- 事件类型：用什么方式触发。如鼠标单击click、鼠标经过mouseover等。
- 事件调用函数：要做什么事。

完成事件监听分成3个步骤：

1. 获取DOM元素
2. 通过 `addEventListener` 方法为DOM节点添加事件监听
3. 等待事件触发，如用户点击了某个按钮时便会触发 `click` 事件类型
4. 事件触发后，相对应的回调函数会被执行

简单描述：所谓的事件无非就是找个机会（事件触发）调用一个函数（回调函数）。

```html
<button>click</button>
<script>
	// 事件源
	const btn = document.querySelector('button');
	// 添加事件类型和事件处理函数
	btn.addEventListener('click', function () {
		alert('你好呀');
	});
</script>
```

事件监听版本：

DOM L0时期，事件监听方式：`事件源.on事件=function(){}`

DOM L2时期，事件监听方式：`事件源.addEventListener('事件',事件处理函数)`

现在推荐使用L2时期的监听方式。on方式会被覆盖，addEventListener方式可以绑定多次，拥有事件更多特性。

```js
	const btn1 = document.querySelector('.btn1');
	const btn2 = document.querySelector('.btn2');
	// 早期的事件监听方式。on事件方式会出现事件覆盖问题
	btn1.onclick = function () {
		alert(11);
	}
	btn1.onclick = function () {
		alert(22);
	}
	// 现在推荐使用的事件监听方式。这种方式可以添加多个事件，拥有更多事件特性
	btn2.addEventListener('click', function () {
		alert('hi~hi');
	});
	btn2.addEventListener('click', function () {
		alert('ha~hi');
	});
```

### 4.2 事件类型

鼠标事件：通过鼠标触发的事件，比如鼠标单击（click）、鼠标经过（mouseenter）、鼠标离开（mouseleave）

焦点事件：表单获得光标，获取焦点（focus）、失去焦点（blur）

键盘事件：键盘触发，键盘按下（keydown）、键盘抬起（keyup）

文本事件：表单输入触发，用户输入事件（input）

。。。

### 4.3 事件对象

任意事件类型被触发时与事件相关的信息会被以对象的形式记录下来，称这个对象为事件对象。

事件回调函数的【第1个参数】即所谓的事件对象，通常习惯性的将这个对数命名为 `event`、`ev`、`e` 。

事件对象常用属性：

1. `e.type` 当前事件的类型
2. `e.clientX/Y` 光标相对浏览器窗口的位置
3. `e.offsetX/Y` 光标相于当前 DOM 元素的位置
3. `e.key` 用户按下的键盘的值。以前叫keyCode，现在不提倡使用，直接用key。

注：在事件回调函数内部通过 window.event 同样可以获取事件对象。

```js
	const btn = document.querySelector('button');
	const input = document.querySelector('input');
	// 事件绑定函数的第一个参数就是事件对象，通常会命名为e、ev、event
	btn.addEventListener('click', function (e) {
		console.log(e);
	});
	input.addEventListener('keyup', function (e) {
		// console.log(e);
		if (e.key === 'Enter') console.log('这是回车键');
	});
```

### 4.4 环境对象（this）

环境对象指的是函数内部特殊的变量 `this` ，它代表着当前函数运行时所处的环境。

1. `this` 本质上是一个变量，数据类型为对象
2. 函数的调用方式不同 `this` 变量的值也不同
3. 【谁调用 `this` 就是谁】是判断 `this` 值的粗略规则

```js
	const btn = document.querySelector('button');
	btn.addEventListener('click', function () {
		console.log(this); // 事件回调函数中，谁调用，this指向谁
		btn.style.width = '100px';
		this.style.color = 'red';
	});

	function fn() {
		console.log(this); // 普通函数中的this指向window
	}

	fn();
```

### 4.5 回调函数

如果将函数 A 做为参数传递给函数 B 时，称函数 A 为回调函数。

回调函数本质还是函数，只不过把它当成参数使用。

使用匿名函数做为回调函数比较常见。

```js
	const btn = document.querySelector('button');
	// 回调函数很多时候是匿名函数
	btn.addEventListener('click', function () {
		btn.style.width = '100px';
		this.style.color = 'red';
	});

	function fn() {
		console.log('fn是回调函数');
	}

	// 把函数当做另一个函数的参数，作为参数的函数称为回调函数。
	setInterval(fn, 1000);
```

### 4.6 事件流

事件流指的是事件完整执行过程中的流动路径。

事件流是对事件执行过程的描述，了解事件的执行过程有助于加深对事件的理解，提升开发实践中对事件运用的灵活度。

![event](https://cdn.jsdelivr.net/gh/yuhuo2022/pic-bed/web/pic202204130158214.png)

如上图所示，任意事件被触发时总会经历两个阶段：【捕获阶段】和【冒泡阶段】。

简言之，捕获阶段是【从父到子】的传导过程，冒泡阶段是【从子向父】的传导过程。

#### 4.6.1 捕获和冒泡

事件捕获：从DOM的根元素开始执行对应的事件（从外到内）。

事件捕获需要写对应代码才能看到效果：`DOM.addEventListener(‘事件类型’,事件处理函数,是否使用捕获机制)`。

addEventListener第三个参数传入true代表捕获阶段触发（很少使用）。若第三个参数传入false代表冒泡阶段触发，默认是false，可以省略。

```js
	const father = document.querySelector('.father');
	const son = document.querySelector('.son');
	// 事件捕获需要给addEventListener传第三个参数true。默认是false。
	document.addEventListener('click', function () {
		alert('爷爷');
	}, true);
	father.addEventListener('click', function () {
		alert('爸爸');
	}, true);
	son.addEventListener('click', function () {
		alert('儿子');
	}, true);
```

**事件冒泡**：当一个元素的事件被触发时，同样的事件将会在该元素的所有祖先元素中依次触发。

简单理解冒泡：当一个元素触发事件后，会依次向上调用所有父级元素的**同名事件**。

事件冒泡是默认存在的。

```js
	// 事件冒泡：当一个元素触发事件后，会依次向上调用所有父级元素的同名事件
	document.addEventListener('click', function () {
		alert('爷爷');
	});
	father.addEventListener('click', function () {
		alert('爸爸');
	});
	son.addEventListener('click', function () {
		alert('儿子');
	});
```

#### 4.6.2 阻止冒泡

阻止冒泡是指阻断事件的流动，保证事件只在当前元素被执行，而不再去影响到其对应的祖先元素。

问题：默认存在冒泡模式，容易影响到父级元素

需求：要将事件限制在当前元素内，阻止冒泡

前提：需要拿到事件对象

语法：`事件对象.stopPropagetion()`

注意：**stopPropagetion()方法可以阻止事件流动传播，在捕获阶段和冒泡阶段都有效。**

```js
	son.addEventListener('click', function (e) {
		alert('儿子');
		// 阻止事件流动（冒泡）
		e.stopPropagation();
	});
```

鼠标经过事件：

- mouseover和mouseout 会有冒泡效果

- mouseenter和mouseleave没有冒泡效果 (推荐)

### 4.7 解绑事件

on事件方式：直接使用null覆盖就可以实现事件的解绑。

addEventListener方式需要使用`removeEventListener('事件类型',事件处理函数[,获取捕获或者冒泡阶段])`解绑事件。

**注意：匿名函数无法被解绑。**

```js
	const btn = document.querySelector('button');
	btn.onclick = function () {
		alert('被点击了');
	}
	// 传统方式事件解绑
	btn.onclick = null;

	btn.addEventListener('click', fn);
	// 解绑事件(匿名函数不能解绑)
	btn.removeEventListener('click', fn);

	function fn() {
		alert('被点击了');
	}
```

### 4.8 事件委托

事件委托是利用事件流的特征解决一些现实开发需求的知识技巧，主要的作用是提升程序效率。

事件委托的好处：减少注册次数，可以提高程序性能。

事件委托原理：利用事件冒泡的特点：

- 给父元素注册事件，触发子元素时会冒泡到父元素身上，从而触发父元素的事件。

事件委托实现：`事件对象.target.tagName`可以获取到真正触发事件的元素

```js
	const ul = document.querySelector('ul');
	// 事件委托方式 点击li，li变红
	ul.addEventListener('click', function (e) {
		// e.target 单击的对象
		// e.target.style.color = 'red'; // 这种方式直接用有问题，会影响其他元素
		// e.target.tagName 触发的真正元素。是大写的标签名
		if (e.target.tagName === 'LI') {
			e.target.style.color = 'red';
		}
	});
```

### 4.9 阻止默认行为

在一些情况下，需要阻止默认行为，如链接跳转、表单域跳转等。

语法：`事件对象.preventDefault()`

```js
	const form = document.querySelector('form');
	form.addEventListener('submit', function (e) {
		// 阻止默认行为
		e.preventDefault();
	});
```

### 4.10 其他事件

#### 4.10.1 页面加载事件

加载外部资源（如图片、外联CSS和JavaScript等）加载完毕时触发的事件。

有些时候需要等页面资源全部处理完了做一些事情。

除了监听整个页面资源加载完毕，也可以针对某个资源绑定load事件。

**事件名：load**

监听页面所有资源加载完毕：

```
window.addEventListener('load', function() {
    // xxxxx
});
```

```html
<script>
	// 等页面资源加载完毕才执行回调
	window.addEventListener('load', function () {
		const btn = document.querySelector('button');
		btn.addEventListener('click', function () {
			alert('被点击了');
		});
	});
</script>
<button>点击</button>
```

**事件名：DOMContentLoaded**

初始的HTML文档被完全加载和解析完成后，DOMContentLoaded事件触发，不需要等待样式表、图片等完全加载。

```js
document.addEventListener('DOMContentLoaded',function(){

});
```

#### 4.10.2 元素滚动事件

滚动条在滚动的时候持续触发的事件。

很多网页在检测到滚动到某个区域后会做一些处理，如固定导航栏，返回顶部等。

**事件名：scroll**

```js
	// 页面滚动事件给window或者document加，必须有滚动条才行
	window.addEventListener('scroll', function () {
		console.log('滚动了');
	});
```

也可以给某些元素加。

光滚动是没什么意义的，需要配合两个属性使用才能实现一些效果。

- scrollLeft 元素内容往左滚出去看不到的距离
- scrollTop 元素内容往上滚出去看不到的距离

这两个属性的值可以读写，含义是获取被卷去的大小。

```html
	<style>
		body {
			height: 1500px;
		}

		div {
			display: none;
			width: 200px;
			height: 100px;
			margin: 100px auto;
			border: 2px solid #F0F;
			overflow: scroll;
		}
	</style>
<body>
<div>
	文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字
	文字文字文字文字文字文字文字文字文字文字文字文字文字文字文字
	文字文字文字文字文字文字文字文字文字文字
</div>
<script>
	const div = document.querySelector('div');
	// 页面滚动事件给window加，必须有滚动条才行
	window.addEventListener('scroll', function () {
		// console.log('滚动了');
		// 页面被卷去了多少
		// document.documentElement获取html标签
		// console.log(document.documentElement.scrollTop); 需要写在函数中，得到的结果是不带单位的数值
		const des = document.documentElement.scrollTop;
		if (des >= 100) {
			div.style.display = 'block';
		} else {
			div.style.display = 'none';
		}
	});
	// 元素滚动
	div.addEventListener('scroll', function () {
		// 被卷去的头部（看不见的部分）
		console.log(div.scrollTop);
	});
</script>
</body>
```

**document.documentElement获取html标签**

滚动到指定坐标：`元素.scrollTo(x,y)`。scrollTo可以把内容滚动到指定的坐标

#### 4.10.3 页面尺寸事件

会在窗口尺寸改变的时候触发事件：

```
window.addEventListener('resize', function() {
    // xxxxx
})
```

获取元素可见部分宽高（不包含边框、外边距、滚动条等）：

- clientWidth和clientHeight

获取元素宽高（包含元素自身设置的宽高、边框、内边距）：

- offsetWidth和offsetHeight
- 获取出来的是数值，方便计算
- 获取的是可视宽高，隐藏的盒子结果为0

获取元素距离自己定位父级元素的左、上距离：

- offsetLeft和offsetTop
- 这两个是只读属性

```js
	// 窗口尺寸改变触发事件
	window.addEventListener('resize', function () {
		console.log(1);
	});
	const div = document.querySelector('div');
	const p = document.querySelector('p');
	console.log(div.offsetLeft);
	// 检测盒子位置，  最近一级带有定位的祖先元素
	console.log(p.offsetLeft);
```

获取元素的位置：`element.getBoundingClientRect()`。这个方法返回元素的大小及其相对于视口的位置

## 五、日期对象Date

ECMAScript中内置了获取系统时间的对象 Date，使用 Date 时与之前内置对象 console 和 Math 不同，它需要借助 new 关键字才能使用。

### 5.1 实例化日期对象

```js
	// 实例化 new
	// 获取当前时间
	const date = new Date();
	console.log(date);

	// 得到指定时间
	const date1 = new Date('2022-10-1 10:30:00');
	console.log(date1);
```

### 5.2 日期对象常用方法

实例化日期对象返回的时间是标准时，绝大多数情况下需要格式化时间，转变成常用格式。

常用格式化时间方法：

getFullYear 获取四位年份

getMonth 获取月份，取值为 0 ~ 11

getDate 获取月份中的每一天，不同月份取值也不相同

getDay 获取星期，取值为 0 ~ 6

getHours 获取小时，取值为 0 ~ 23

getMinutes 获取分钟，取值为 0 ~ 59

getSeconds 获取秒，取值为 0 ~ 59

```js
	// 按照格式显示时间
	function formatDate() {
		const date = new Date();
		const month = date.getMonth() + 1 >= 10 ? date.getMonth() + 1 : '0' + (date.getMonth() + 1);
		const hours = date.getHours() >= 10 ? date.getHours() : '0' + date.getHours();
		const minutes = date.getMinutes() >= 10 ? date.getMinutes() : '0' + date.getMinutes();
		const seconds = date.getSeconds() >= 10 ? date.getSeconds() : '0' + date.getSeconds();
		return `今天是：${date.getFullYear()}年-${month}月-${date.getDate()}日  ${hours}:${minutes}:${seconds}`;
	}

	const div = document.querySelector('div');
	div.innerHTML = formatDate();
	setInterval(function () {
		div.innerHTML = formatDate();
	}, 1000);
```

以上的方法可以根据需要自定义返回的格式。

此外还有一些方法可以返回固定的格式：

toLocaleString():返回日期和时间

toLocaleDateString()：返回日期

toLocaleTimeString()：返回时间

```js
	const div = document.querySelector('div');
	const date = new Date();
	div.innerHTML = date.toLocaleString(); // => 2022/5/12 14:59:44
	// div.innerHTML = date.toLocaleDateString(); // => 2022/5/12
	// div.innerHTML = date.toLocaleTimeString(); // => 14:59:44
	setInterval(function () {
		const date = new Date();
		div.innerHTML = date.toLocaleString();
	}, 1000);
```

### 5.3 时间戳

时间戳是指1970年01月01日00时00分00秒起至现在的总秒数或毫秒数，它是一种特殊的计量时间的方式。

注：ECMAScript中时间戳是以毫秒计。

获取时间戳的三种方式：

1. getTime()方法
2. +new Date()
3. Date.now()  不需要实例化，但只能得到当前时间戳。其他两种可以返回指定时间戳。

```js
	// 获取时间戳
	// 1
	const date = new Date();
	console.log(date.getTime());
	// 2
	console.log(+new Date());
	// 3 不需要实例化。但只能得到当前时间戳。其他两种可以返回指定时间戳
	console.log(Date.now());

	// 指定时间戳
	console.log(+new Date('2022-5-15'));
```

时间戳的典型应用场景就是倒计时。

算法：

- 将来的时间戳 - 现在的时间戳 = 剩余时间毫秒数
- 剩余时间毫秒数转为剩余秒数
- 秒数转为天、时、分、秒

转换公式：

天：秒数/60/60/24

时：秒数/60/60%24

分：秒数/60%60

秒：秒数%60

 ~~(number) 通过位运算取整，速度快

```js
	function getCountTime() {
		// 当前时间戳
		const now = +new Date();
		// 将来时间戳
		const last = +new Date('2022-5-12 18:30:00');
		// 剩余时间戳转为秒数
		const second = (last - now) / 1000;
		// 转为时分秒
		// ~~(number) 通过位运算取整，速度快
		let h = ~~(second / 60 / 60 % 24);
		h = h >= 10 ? h : '0' + h;
		let m = ~~(second / 60 % 60);
		m = m >= 10 ? m : '0' + m;
		let s = ~~(second % 60);
		s = s >= 10 ? s : '0' + s;
		document.querySelector('#hour').innerHTML = h;
		document.querySelector('#minutes').innerHTML = m;
		document.querySelector('#seconds').innerHTML = s;
	}

	getCountTime();
	// 倒计时
	setInterval(getCountTime, 1000);
```

## 六、DOM节点操作

DOM操作不只可以操作元素的属性和文本，对于元素自身也是可以进行各种操作的。

但MVVM框架大行其道的现在，DOM节点操作被弱化，了解就行，不需要很深入研究。

节点的操作主要有：查找、插入、删除、复制、替换等。

### 6.1 DOM节点

DOM树中的每一个内容都称之为节点。

节点类型：

- **元素节点**：所有标签都是元素节点，html是根节点
- 属性节点：所有属性都是属性节点
- 文本节点：所有的文本
- 其他节点

### 6.2 查找节点

#### 6.2.1 查找父节点

`子元素.parentNode`属性，返回最近一级的父节点，找不到返回null

```html
<div class="grandpa">
	<div class="dad">
		<div class="baby"></div>
	</div>
</div>
<script>
	const baby = document.querySelector('.baby');
	// 子元素.parentNode
	console.log(baby.parentNode); // 返回dom对象
</script>
```

#### 6.2.2 查找子节点

`父元素.children`属性仅获取所有元素节点，返回一个伪数组，这个很重要。

父元素.childNodes，获取所有的子节点，包括文本节点（空格、换行）、注释节点等。

```html
<ul>
	<li>
		<p>这是第一个段落</p>
	</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
	<li>5</li>
</ul>
<script>
	const ul = document.querySelector('ul');
	console.log(ul.children);// 返回伪数组，选择的是'亲儿子'
</script>
```

#### 6.2.3 查找兄弟节点

上一个兄弟节点：nextElementSibling属性

下一个兄弟节点：previousElementSibling属性

```html
<ul>
	<li>
		<p>这是第一个段落</p>
	</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
	<li>5</li>
</ul>
<script>
	const li2 = document.querySelector('ul li:nth-child(2)');
	console.log(li2.previousElementSibling); // 上一个兄弟
	console.log(li2.nextElementSibling); // 下一个兄弟
</script>
```

### 6.3 增加节点

很多情况下需要新值节点，如发布一条新评论等。

步骤：

1. 创建一个新节点
2. 把新节点放入到指定的元素内部

创建元素节点：`document.createElement('标签名')`；

创建完后要在页面中看到，需要插入到某个父元素中：

1. 插入到父元素的最后一个子元素：`父元素.appendChild(要插入的元素)`
2. 插入到父元素中某个子元素的前面：`父元素.insertBefore(要插入的元素，在哪一个元素前面)`

```html
<ul>
	<li>老大</li>
</ul>
<script>
	const ul = document.querySelector('ul');
	// 创建节点
	const li = document.createElement('li');

	li.innerHTML = '这是新增的个节点';
	
	// 新值节点插入到某个父元素的最后
	// ul.appendChild(li);
	// 新节点插入到某个父元素的某个子元素之前
	ul.insertBefore(li, ul.children[0]); // 作为ul的第一个子元素
</script>
```

增加节点中有一种特殊情况是复制节点。

复制节点步骤：

1. 复制一个原有节点
2. 把复制的节点放入指定的元素内部

克隆节点：`元素.cloneNode(布尔值)`。cloneNode会克隆一个和原节点一样的元素，括号内传入布尔值。

true：克隆时会把后代节点一起克隆

false：克隆时不克隆后代节点

默认false

```html
<ul>
	<li>1</li>
	<li>2</li>
	<li>3</li>
</ul>
<script>
	const ul = document.querySelector('ul');
	// 克隆节点 元素.cloneNode()
	const li1 = ul.children[0].cloneNode(true);
	// 追加
	ul.appendChild(li1);
</script>
```

### 6.4 删除节点

在JS原生DOM操作中，要删除元素必须通过**父元素**删除。

语法：`父元素.removeChild(要删除的元素)`

必须存在父子关系，否则不能删除。

删除节点和隐藏节点是有区别的，隐藏节点还存在，删除节点则是从html中删除。

```html
<div style="display: none">123</div>
<ul>
	<li>无了</li>
</ul>
<script>
	const ul = document.querySelector('ul');
	// 删除元素必须通过父元素来操作
	ul.removeChild(ul.children[0]);
</script>
```

## 七、M端事件

M端就是移动端。

移动端有独特的事件，如触屏事件touch（触摸事件）。

touch对象代表一个触摸点。触摸点可能是一根手指，也可能是一根触摸笔。触摸事件可响应用户手指对屏幕或者触控板操作。

常见的触屏事件：

- touchstart：手指触摸到一个DOM元素触发
- touchmove：手指在一个DOM元素上滑动时触发
- touchend：手指从一个DOM元素上移开时触发

## 八、插件

插件就是别人封装好的一些功能，只需要复制对应代码，就可以直接实现对应效果。

插件的使用过程都差不多：

1. 熟悉官网，了解插件可以完成什么需求
2. 看在线例子，找到符合需求的demo
3. 看基本使用流程
4. 查看API文档，配置自己的插件

这里以swiper为例，了解插件的使用。

打开swiper官网：https://www.swiper.com.cn/

下载需要的版本：https://www.swiper.com.cn/download/index.html

## 九、重绘和回流

浏览器界面渲染步骤：

1. 解析（Parser）HTML，生成DOM树（DOM Tree）
2. 同时解析（Parser）CSS，生成样式规则（Style Rules）
3. 根据DOM树和样式规则生成渲染树（Render Tree）
4. 进行布局Layout（回流/重排）：根据生成的渲染树，得到节点的几何信息（位置、大小）
5. 进行重绘（Painting）：根据计算和获取的信息进行整个页面的绘制
6. Display：展示在页面上

回流（重排）：当渲染树中部分或者全部元素的尺寸、结构、布局等发生变化时，浏览器会重新渲染部分或全部文档的过程。

重绘：节点的样式改变，但不影响在文档流中的位置和文档布局。

**重绘不一定引起回流，回流一定引起重绘。**

导致回流的操作：

- 页面的首次刷新
- 浏览器窗口大小发生改变
- 元素大小或者位置发生改变
- 字体大小改变
- 内容变化
- 伪类激活
- 脚本操作DOM（添加或者删除可见DOM元素）

简单理解就是影响到布局的都会回流。

## 十、BOM

BOM：Browser Object Model 浏览器对象模型。

window对象是一个全局对象，也可以说是客户端JavaScript中的顶级对象。

window包含了document、navigator、location、history、screen、定时器等。

所有通过var定义在全局作用域中的变量、函数都会成为window对象的属性和方法。

window对象下的属性和方法在调用的时候可以省略window。

### 10.1 location对象

location的数据类型是对象，它拆分并保存了URL地址中的各个组成部分。

常用属性和方法：

- **href**属性获取完整的URL地址，对其赋值时用于地址跳转

```js
	const a = document.querySelector('a');
	let num = 5;
	let timer = setInterval(function () {
		num--;
		a.innerHTML = `${num}秒后自动跳转到百度首页`;
		if (num === 0) {
			// 清理定时器
			clearInterval(timer);
			// 跳转页面
			location.href = 'https://www.baidu.com';
		}
	}, 1000);
```

- **search**属性获取完整地址中携带的参数，符号？后面的部分
- **hash**属性获取地址中的哈希值，符号#后面的部分
- **reload**方法用来刷新当前页面，传入参数true时表示强制刷新

```html
<form action="">
	<label>
		用户名：<input name="uname" type="text">
	</label>
	<label>
		密码：<input name="pwd" type="password">
	</label>
	<button>提交</button>
</form>
<body>
<a href="#/my">我的</a>
<script>
	console.log(location.search); // => ?uname=123&pwd=123
	console.log(location.hash); // => 这个是用于学路由（VUE）做铺垫，经常用于不刷新页面，显示不同页面
	const btn = document.querySelector('button');
	btn.addEventListener('click', function () {
		// 刷新页面 传入产生true，强制刷新
		location.reload();
	});
</script>
```

### 10.2 navigator对象

navigator对象记录了浏览器自身的相关信息。

常用的属性和方法：

- userAgent：检测浏览器的版本和平台

```js
	const btn = document.querySelector('button');
	btn.addEventListener('click', function () {
		console.log(navigator);
		// navigator.userAgent常用来判断浏览器信息
		console.log(navigator.userAgent);
	});
```

### 10.3 history对象

history对象主要管理历史记录，该对象与浏览器地址栏的操作相对应。如前进、后退、历史记录等

常用的方法：

- back(),后退
- forward()，前进
- go(参数)，前进后退，参数为1，前进1个页面，-1，后退一个页面

```js
	const back = document.querySelector('.back');
	const forward = document.querySelector('.forward');

	back.addEventListener('click', function () {
		// history.back();
		history.go(-1);
	});
	forward.addEventListener('click', function () {
		// history.forward();
		history.go(1);
	});
```

history现在用的不多

### 10.4 本地存储

互联网的快速发展，基于网页的应用越来越普遍，也越来越复杂，为了满足各种需求，会经常性在本地存储大量的数据。HTML5规范提出了相关的解决方案。

本地存储：

1. 数据存储在**用户浏览器**中
2. 设置、读取方便，甚至页面刷新不丢失
3. 容量较大，sessionStorage和localStorage约5M左右
3. 本地存储只能存字符串

分类：

- `localStorage`：将数据永久存储在本地（用户的电脑），除非手动删除，否则页面关闭也会存在

  - 可以多窗口（页面）共享【同一浏览器可以共享】
  - 以键值对的形式存储使用
  - 存储数据：`localStorage.setItem(key,value)`
  - 读取数据：`localStorage.getItem(key)`
  - 删除一条数据：`localStorage.removeItem(key)`
  - 清空所有数据：`localStorage.clear()`慎用。

```js
  	// 存储(修改)
  	localStorage.setItem('uname', '苏白');
  	// 读取
  	console.log(localStorage.getItem('uname'));
  	// 删除一条
  	localStorage.removeItem('uname');
  	// 清空所有
  	localStorage.clear();
```

- `sessionStorage`
  - 生命周期为关闭浏览器窗口
  - 在同一个窗口下数据可以共享
  - 以键值对的形式存储
  - 用法和localStorage基本相同

```js
	// 存储
	sessionStorage.setItem('uname', '苏白');
	// 读取
	console.log(sessionStorage.getItem('uname'));
	// 删除一条
	sessionStorage.removeItem('uname');
	// 清空所有
	// sessionStorage.clear();
```


#### 10.4.1 存储复杂数据类型

本地存储只能存字符串，无法直接存储复杂数据类型。

存储复杂数据类型，需要转为JSON字符串后再存储到本地。

语法：`JSON.stringify(复杂数据类型)`

获取转换的JSON字符串要使用需要转为普通对象：

语法：`JSON.parse()`

```js
	const obj = {
		uname: '苏白',
		uAge: 24,
		gender: '男'
	}
	// 本地存储无法直接存储复杂的数据类型
	// localStorage.setItem('obj', obj);
	// console.log(localStorage.getItem('obj')); // [object Object]

	// 复杂类型转为JSON字符串后存储
	localStorage.setItem('obj', JSON.stringify(obj));
	console.log(localStorage.getItem('obj')); // {"uname":"苏白","uAge":24,"gender":"男"}
	
// 获取存储的复杂数据类型需要将JSON字符串对象转为普通对象类型
	console.log(JSON.parse(localStorage.getItem('obj')));
```

## 十一、JS执行机制

JS的一大特点就是**单线程**，即**同一时间只能做一件事**。

这是JS诞生的使命所致，JS是为了处理页面中用户交互以及操作DOM而诞生的。

如对某个DOM元素进行添加和删除操作，不能同时进行，要先添加，然后再删除。

单线程意味着所有任务需要排队，前一个任务结束，才会执行后一个任务，这就导致了JS执行时间过长会造成页面的渲染不连贯，导致页面渲染加载阻塞。

为了解决这个问题，利用多核CPU的计算能力，HTML5提出了Web Worker标准，允许JS脚本创建多个线程，于是JS出现了**同步**和**异步**。

同步：前一个任务结束后再执行后一个任务，程序的执行顺序与任务的排列顺序一致，是同步的。如：一个锅在炒第一个菜的同时，这个锅不能用来炒第二个菜，要等第一个菜炒好，才能用这个锅炒第二个菜。

异步：做一件事时，因为这件事会花费很长时间，在做这件事的同时，还可以去处理其他的事情。如烧开水的过程中可以去煮饭、切菜等。

本质区别：执行顺序不同。

同步任务都是在主线程上执行，形成一个执行栈。

异步任务：JS的异步是通过回调函数实现的，如定时器。

一般而言，异步任务有三种类型：

- 普通事件，如click、resize等。
- 资源加载，如load、error等。
- 定时器等。

异步任务相关添加到任务队列中（也称为消息队列）。

JS执行机制：

1. 先执行**执行栈中的同步任务**。
2. 异步任务放入任务队列。
3. 一旦执行栈中的同步任务执行完毕，系统会按次序读取任务队列中的异步任务，于是被读取的异步任务结束等待状态，进入执行栈，开始执行。

```js
	console.log(1);
	console.log(2);
	setTimeout(function () {
		console.log(3)
	}, 1000);
	console.log(4);
```

- 执行上面代码时，打印1，2，4会进入执行栈，定时器打印3会进入任务队列。
- 当打印1，2，4执行完后，程序会执行任务队列中的任务，打印4。
- 所以，上面代码的打印顺序是1，2，4，3。

注意，JS是单线程，但浏览器是多线程的。

**由于主线程不断的重复获得任务、执行任务、再获取任务、再执行，所以这种机制被称为`事件循环(event loop)`。**

## 十二、正则表达式

正则表达式是用于匹配字符串中字符组合的模式。

JS中的正则表达式是对象。

正则表达式通常用于查找、替换符合正则表达式的文本。

很多语言都支持正则表达式。

JS中的正则经常用于表单验证、敏感词过滤、提取特定内容等。

### 12.1 正则的定义与使用

正则两步：

1. 定义规则
2. 匹配内容

JS中定义正则表达式语法有两种：

```
方法1:
const 变量名 = /表达式/
```

`//`是正则表达式的字面量

匹配规则：

`正则规则.test(被检测的字符串)`用来查看正则表达式与指定字符串是否匹配。返回值是布尔值。

`正则规则.exec(被检测的字符串)`在一个指定字符串中执行一个搜索匹配，匹配成功，返回一个数组，否则返回null。

**多使用test方法**

```js
	// 被匹配的字符串
	const str1 = 'java需要了解一些基本的规则，前端也是要知道一些java知识的';
	const str2 = '前端不需要掌握后端语言';
	// 定义正则规则
	const reg = /java/;
	// 匹配 reg.test(被检测的内容)
	console.log(reg.test(str1)); // true
	console.log(reg.test(str2)); // false
	console.log(reg.exec(str1)); // ['java', index: 0, input: 'java需要了解一些基本的规则，前端也是要知道一些java知识的', groups: undefined]
```

### 12.2 元字符

普通字符：大多数能够描述自身的字符被称为普通字符，如所有的字母和数字。简单理解为普通字符只能够匹配字符串中与它们相同的字符。

元字符：一些具有特殊含义的字符，可以极大提高匹配的灵活性。

参考文档：[正则表达式 - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)

正则测试工具：[在线正则表达式测试 (oschina.net)](https://tool.oschina.net/regex)

为了便于学习和记忆，可以对众多元字符进行分类：

- 边界符:表示位置，开头和结尾，必须用什么开头，用什么结尾
- 量词：表示重复次数
- 字符类：如\d表示0～9，是对量词的简化

#### 12.2.1 边界符

正则表达式中的边界符用来**提示字符所处的位置**，主要有两个字符：

| 边界符 |              说明              |
| :----: | :----------------------------: |
|   ^    | 表示匹配行首的文本（以谁开始） |
|   $    | 表示匹配行尾的文本（以谁结束） |

**为了精确匹配，`^和$`都会写上**

```js
	// 元字符的边界符
	console.log(/嘿嘿/.test('嘿嘿哈哈')); // true
	console.log(/嘿嘿/.test('哈哈嘿嘿')); // true
	console.log(/嘿嘿/.test('啊嘿嘿哈哈')); // true
	console.log('---------------------');
	// 以嘿嘿开头
	console.log(/^嘿嘿/.test('嘿嘿哈哈')); // true
	console.log(/^嘿嘿/.test('哈哈嘿嘿')); // false
	console.log('---------------------');
	// 以嘿嘿结束
	console.log(/嘿嘿$/.test('嘿嘿哈哈')); // false
	console.log(/嘿嘿$/.test('哈哈嘿嘿')); // true
	// 为了精确匹配，开头和结尾都会写上
	console.log(/^哈嘿$/.test('哈哈嘿嘿')); // false
```

#### 12.2.2 量词

量词用来**设定某个模式出现的次数**

| 量词  | 说明          |
| ----- | ------------- |
| *     | 重复0次或更多 |
| +     | 重复1次或更多 |
| ？    | 重复0次或1次  |
| {n}   | 重复n次       |
| {n,}  | 重复n次或更多 |
| {n,m} | 重复n到m次    |

量词有两组写法，花括号的写法更常用。

```js
// *号重复0次或多次
	console.log(/^哈$/.test('哈')); // true
	console.log(/^哈*$/.test('哈')); // true
	console.log(/^哈*$/.test('哈哈哈')); // true
	console.log(/^哈*$/.test('二哈哈哈')); // false
	console.log(/^哈*$/.test('哈很哈哈')); // false
	console.log('----------------------');
	// +号重复1次或多次
	console.log(/^哈$/.test('哈')); // true
	console.log(/^哈+$/.test('哈哈')); // true
	console.log(/^哈+$/.test('哈哈哈')); // true
	console.log(/^哈+$/.test('二哈哈哈')); // false
	console.log(/^哈+$/.test('哈很哈哈')); // false
	console.log('----------------------');
	// ？号重复0次或1次
	console.log(/^哈$/.test('')); // false
	console.log(/^哈?$/.test('哈')); // true
	console.log(/^哈?$/.test('哈哈哈')); // false
	console.log(/^哈?$/.test('二哈哈哈')); // false
	console.log(/^哈?$/.test('哈很哈哈')); // false
```

大括号写法：

```js
// 精准匹配
	// {n}，必须出现次数
	console.log(/^哈{3}$/.test('')); // false
	console.log(/^哈{3}$/.test('哈')); // false
	console.log(/^哈{3}$/.test('哈哈')); // false
	console.log(/^哈{3}$/.test('哈哈哈')); // true
	console.log(/^哈{3}$/.test('哈哈哈哈')); // false
	console.log('----------------------');
	// {n,} 大于等于n次
	console.log(/^哈{3,}$/.test('')); // false
	console.log(/^哈{3,}$/.test('哈')); // false
	console.log(/^哈{3,}$/.test('哈哈')); // false
	console.log(/^哈{3,}$/.test('哈哈哈')); // true
	console.log(/^哈{3,}$/.test('哈哈哈哈')); // true
	console.log('----------------------');
	// {n,m} n次到m次，逗号两侧不能有空格
	console.log(/^哈{3,5}$/.test('')); // false
	console.log(/^哈{3,5}$/.test('哈')); // false
	console.log(/^哈{3,5}$/.test('哈哈')); // false
	console.log(/^哈{3,5}$/.test('哈哈哈')); // true
	console.log(/^哈{3,5}$/.test('哈哈哈哈')); // true
	console.log(/^哈{3,5}$/.test('哈哈哈哈哈')); // true
	console.log(/^哈{3,5}$/.test('哈哈哈哈哈哈')); // false
```

#### 12.2.3 字符类

1. []

- `[]`匹配字符集合：字符串中只要包含集合内任意一个字符都返回true

```js
	// [] 出现集合内任意一个字符都能成功匹配  [abc]=== a || b|| c
	console.log(/^[abc]$/.test('a')); // true
	console.log(/^[abc]$/.test('b')); // true
	console.log(/^[abc]$/.test('c')); // true
	console.log(/^[abc]$/.test('ac')); // false
	console.log(/^[abc]{2}$/.test('ac')); // true
```

- `[]`里加入连字符`-`表示范围。如[a-z]表示a-z26个英文字母，[a-zA-Z]大小写字母都可以

```js
	console.log(/^[a-z]$/.test('a')); // true
	console.log(/^[A-Z]$/.test('A')); // true
	console.log(/^[a-zA-Z0-9]$/.test('2')); // true
	console.log(/^[a-zA-Z0-9]$/.test('a')); // true
	console.log(/^[a-zA-Z0-9]$/.test('P')); // true
```

- `[]`里加`^`排除某些字符。如`[^a-z]`匹配除了小写字母外的字符

```js
	console.log(/^[^a-z]$/.test('a')); // false
	console.log(/^[^a-z]$/.test('A')); // true
```

2. `.`号匹配除换行符外的任何单个字符

3. 预定义类：常见模式的简写形式

| 预定类 | 说明                                                       |
| ------ | ---------------------------------------------------------- |
| `\d`   | 匹配0-9之间的任意数字，相当于[0-9]                         |
| `\D`   | 匹配所有0-9以外的字符，相对于`[^0-9]`                      |
| `\w`   | 匹配任意的字母数字下划线，相当于[A-Za-z0-9_]               |
| `\W`   | 匹配除了字母数字下划线以外的字符，相当于`[^A-Za-z0-9_]`    |
| `\s`   | 匹配空格，包括换行符、制表符、空格符等，相当于[\t\r\n\v\f] |
| `\S`   | 匹配非空格字符，相当于`[^\t\r\n\v\f]`                      |

### 12.3 修饰符

修饰符约束正则执行的某些细节行为，如是否区分大小写、是否支持多行匹配等。

语法：`/表达式/修饰符`

`i` 是单词 ignore 的缩写，正则匹配时字母不区分大小写

`g `是单词 global 的缩写，匹配所有满足正则表达式的结果

`replace()` 替换：字符串.replace(正则表达式，‘要替换的文本’)

```js
	console.log(/^java$/.test('java')); // true
	console.log(/^java$/.test('JAVA')); // false
	// 修饰符 i 忽略大小写
	console.log(/^java$/i.test('JAVA')); // true

	const str = 'java是开发技术，学好JAVA可以找到高薪工作 ';
	// g是全局  字符串.replace(正则表达式, '替换文本') 是替换
	const result = str.replace(/java/ig, '前端');
	// 正则中的或是｜，一个竖线
	// const result = str.replace(/java｜JAVA/g, '前端');
	console.log(result);
```

