## 一、作用域

作用域（scope）是变量能够访问的范围，离开了这个范围，变量就不能被访问。

作用域分为**局部作用域**和**全局作用域**。

### 1.1 局部作用域

局部作用域有两种：函数作用域和块作用域。

函数作用域：

- 在函数中声明的变量只能在函数内部访问，外部无法直接访问。
- 函数的参数也是函数内部的局部变量。
- 不同函数内部声明的变量无法互相访问。
- 函数执行完毕，函数内部的变量实际被清空了。

块作用域：

- JS中使用{}包裹的代码称为代码块，代码块内部声明的变量外部**有可能**无法访问。
- let声明的变量会产生块作用域，var声明的变量不会产生块作用域。
- const声明的常量也会产生块作用域。
- 不同代码块之间的变量无法互相访问。
- 开发中推荐优先使用const，然后是let，尽量不要用var。

### 1.2 全局作用域

script标签和js文件的最外层就是全局作用域，在此声明的变量在函数内部也可以被访问。

全局作用域中声明的变量在其他任何作用域中都可以被访问。

为window对象动态添加的属性默认是全局的，不推荐使用。

函数中没有任何关键字声明的变量也是全局变量，不要这样使用。

尽可能的少声明全局变量，防止全局变量污染。

### 1.3 作用域链

作用域链本质是底层的**变量查找机制**。

在函数被执行时，会**优先查找当前**函数作用域中的变量，当前作用域中找不到则会**依次逐级查找父作用域**，直到全局作用域。

嵌套关系的作用域串联起来形成了作用域链。

相同作用域链中按照从小到大的规则查找变量。

子作用域能够访问父作用域，父作用域无法访问子作用。

### 1.4 JS垃圾回收机制

垃圾回收机制（Garbage Collection）简称GC。

JS中内存的分配和回收都是自动完成的，内存在不使用的时候会被垃圾回收器自动回收。

因为垃圾回收器的存在，很多人认为JS不需要太关心内存管理的问题。但不了解JS的内存管理机制很容易造成内存泄漏问题（不再使用的内存没有及时释放）。

#### 1.4.1 内存的生命周期

JS中分配的内存生命周期：

1. 内存分配：声明变量、函数或者对象时，系统自动分配内存
2. 内存使用：使用变量、函数等
3. 内存回收：使用完毕，由垃圾回收器自动回收不再使用的内存

注意：全局变量一般不会回收，只有结束运行才会回收。一般情况下，局部变量的值不用了就会自动回收。

#### 1.4.2 垃圾回收算法

垃圾回收的核心就是如何判断内存是否不会再被使用，不会使用就回收。

常见的浏览器垃圾回收算法有**引用计数法**和**标记清除法**。

引用计数法：

- IE采用的这种算法，定义内存不再使用的标准就是看一个对象是否有指向它的引用。
- 算法：
  - 跟踪记录每个值被引用的次数
  - 值被引用一次就记录次数加1
  - 多次引用会累加
  - 减少一个引用就减1
  - 引用次数为0，则释放内存。
- 缺陷：嵌套引用，如果两个对象相互引用，尽管不再使用，垃圾回收器也不会进行回收，导致内存泄漏。
- 现代的浏览器已经不再使用引用计数法了，现在通用的是基于标记清除算法的某些改进算法，总体思想还是一致的。

标记清除法：

- 标记清除算法将不再使用的对象标记为无法达到的对象
- 从根部（JS中是全局对象）出发，定时扫描内存中的对象，凡是能从根部到达的对象都是需要使用。
- 无法从根部出发触及的对象被标记为不再使用，稍后进行回收

### 1.5 闭包

闭包：一个函数对周围状态的引用捆绑在一起，内层函数中访问到其外层函数的作用域。

简单理解：闭包 = 内层函数 + 外层函数的变量

![image-20220624143057257](https://cdn.jsdelivr.net/gh/yuhuo2022/pic-bed/web/pic202206241431780.png)

闭包作用：封闭数据，提供操作，外部也可以访问函数内部的变量。

闭包常见形式：

```js
	// 常见闭包形式：外部可以访问使用函数内部的变量
	function outer() {
		let num = 10;

		function inner() {
			console.log(num);
		}

		return inner;
	}

	// outer() === inner === function inner(){}  都是等价的
	// const fun = function inner() {};
	const fun = outer();
	fun(); //外部要使用num   函数调用
```

另一种常见写法：

```js
	function outer() {
		let num = 100;
		return function () {
			console.log(num);
		}
	}
```

第三种常见写法：

```js
	// 第三种常见写法
	function outer() {
		let num = 100;
		return function () {
			return num;
		}
	}

	const fun = outer();
	console.log(fun());
```

闭包应用：实现数据私有

```js
	// 统计函数调用的次数
	let i = 0; // i是全局变量，有被篡改的风险

	function fn() {
		i++;
		console.log(`函数被调用了${i}次`);
	}

	// 闭包形式
	function count() {
		let i = 0; // 局部变量，不能直接修改，更安全
		function fn() {
			i++;
			console.log(`函数被调用了${i}次`);
		}

		return fn;
	}

	const fun = count();

```

**闭包可能有内存泄漏问题**

### 1.6 变量提升

变量提升是JS中比较奇怪的现象，仅存在于var声明的变量中，它允许变量声明之前被访问，这不是一件好事。

var声明的变量会在程序执行时被提升到**当前作用域的最前面，只提升声明，不提升赋值**。

注意：

- 变量在未声明被访问时会报语法错误
- 变量在var声明之前被访问，变量的值是undefined
- let/const声明的变量和常量不存在变量提升
- 变量提升只在相同作用域中
- **开发中推荐先声明变量，后使用变量。优先使用const，然后let，尽量不用var**

## 二、函数进阶

### 2.1 函数提升

函数提升类似于变量提升，是指函数在声明之前就能被调用。

JS代码执行前，会将函数声明提升到当前作用域的最前面。只提升函数声明，不提升函数调用。

注意函数表达式，函数表达式必须先声明和赋值，后调用，否则会报错。

函数提升能够让函数的声明更灵活，是能被接受的。

### 2.2 函数参数

函数参数有很多使用细节，熟练掌握可以提高函数的灵活度。

函数参数可以分很多种：形式参数、实际参数、默认参数、动态参数、剩余参数

#### 2.2.1 动态参数

动态参数`arguments`是函数内部内置的伪数组变量，包含了调用函数时传入的所有实参。

当不确定要传入多少实参是可以用动态参数。

```js
	function getSum() {
		let sum = 0;
		// arguments 只存在于函数内部的动态参数  arguments是伪数组
		for (let i = 0; i < arguments.length; i++) {
			sum += arguments[i];
		}
		console.log(sum);
	}

	getSum(1, 2);
	getSum(1, 2, 3);
	getSum(2, 2, 3, 4);
```

#### 2.2.2 剩余参数

剩余参数允许将不定数量的参数表示为一个数组。

剩余参数的表现形式：`...`。如：...arr。

三个点是语法符号，置于最末函数形参之前，用于获取多余的实参。

借助...获取的剩余参数是真数组。

开发中更多使用剩余参数，不使用动态参数。

```js
	function getSum(...arr) {
		let sum = 0;
		for (let i = 0; i < arr.length; i++) {
			sum += arr[i];
		}
		console.log(sum);
	}

	getSum(1, 2);
	getSum(1, 2, 3);
	getSum(2, 2, 3, 4);
```

注意：剩余参数的语法符合和展开运算符一样都是三个点。

#### 2.2.3 拓展——展开运算符

展开运算符（...）可以将一个数组或者对象进行展开。

展开运算符不会修改原数组，典型的运用场景是求数组最值或者合并数组。

```js
	// 展开运算符可以展开数组或者对象，一般多用于数组
	const arr1 = [1, 2, 3, 5];
	const arr2 = [6, 7, 4];
	console.log(arr1); // (4)[1, 2, 3, 5]
	console.log(...arr1); // 1 2 3 5

	// 典型应用：求数组元素最值或者合并数组
	console.log(Math.max(...arr1));
	const arr = [...arr1, ...arr2];
	console.log(arr); // [1, 2, 3, 5, 6, 7, 4]
```

### 2.3 箭头函数

箭头函数是函数的一种简单写法，可以说是一种语法糖。箭头函数最合适用来写匿名函数。

#### 2.3.1 基本语法

箭头函数一般用在匿名函数。

只有一个参数可以省略小括号。

如果函数体只有一行代码，可以写到一行上，并且无需写return，直接返回值。

加括号的函数体返回对象字面量表达式。

```js
	// 箭头函数基本写法
	const fun = (a, b) => {
		console.log(a + b);
	}

	// 只有一个参数时可以省略小括号
	const fun1 = a => {
		console.log(a);
	}
	// 只有一行代码时可以省略大括号
	const fun2 = a => console.log(a);

	// 只有一行代码可以省略return
	const fun3 = a => a + a;

	// 箭头函数可以直接返回一个对象  用小括号括起
	const fun4 = (uname) => ({uname});
```

#### 2.3.2 箭头函数参数

箭头函数没有arguments动态参数，但有剩余参数。

```js
	const getSum = (...arr) => {
		let sum = 0;
		for (let i = 0; i < arr.length; i++) {
			sum += arr[i];
		}
		return sum;
	}
	console.log(getSum(2, 3));
```

#### 2.3.3 箭头函数this

箭头函数出现之前，每一个新函数根据它是**如何调用**的来定义这个函数的this，很烦。粗暴理解就是谁调用指向谁。

**箭头函数不会创建自己的this**，只会从自己的作用域链的上一层沿用this。

```js
	// 箭头函数的this是上一层作用域的this指向
	const fn = () => {
		console.log(this); // window
	}
	fn();

	// 对象方法箭头函数this
	const obj = {
		sayHi: () => {
			console.log(this); // window
		}
	}
	obj.sayHi();

	const obj1 = {
		sayHi: function () {
			console.log(this); // obj1
			let i = 10;
			const count = () => {
				console.log(i);
				console.log(this); // obj1
			}
			count();
		}
	}
	obj1.sayHi();
```

开发中使用箭头函数前要考虑函数中this的值，事件回调函数使用箭头函数时，this指向全局的window，因此DOM事件回调函数为了简便，不推荐使用箭头函数。

## 三、解构赋值

### 3.1 数组解构

数组解构是将数组的元素值快速批量赋值给一系列变量的简洁语法。

基本语法：

- 赋值号=左侧的[]用于批量声明变量，右侧数组的元素值将被赋值给左侧的变量
- 变量的顺序对应数组元素的位置，依次进行赋值操作

```js
	// const arr = [100, 60, 80];
	// 数组解构赋值
	const [max, min, avg] = [100, 60, 80];
	console.log(max);
	console.log(min);
	console.log(avg);

	//典型应用：交换变量值
	let a = 1;
	let b = 2;
	[a, b] = [b, a];
	console.log(a, b);
```

有些时候，解构变量和数组元素的个数不是一一对应的，这需要注意：

- 变量多，元素少，没有被赋值的变量是undefined。

```js
// 变量多，元素少，没有被赋值的变量是undefined
	const [a, b, c, d] = [1, 2, 3];
	console.log(a, b, c, d); //  1 2 3 undefined
```

- 变量少，元素多，多余的元素值可以用剩余参数处理或者不处理。

```js
	// 变量少，元素多，多余的元素值不进行赋值。这种情况可以使用剩余参数解决
	const [e, f, ...g] = [4, 5, 6, 7];
	console.log(e, f); // 4 5
	console.log(g); // [6,7]
```

- 可以给解构变量赋默认值，防止undefined传递。

```js
	const [m = 8, n = 9] = [];
	console.log(m, n); // 8 9
```

- 按需导入赋值

```js
	const [h, i, , k] = [10, 11, 12, 13]
	console.log(h, i, k); // 10 11 13
```

- 多维数组解构

```js
	const [a, b, [c, d]] = [1, 2, [3, 4]];
	console.log(a, b, c, d);
```

### 3.2 对象解构

对象解构是将对象属性和方法快速批量赋值给一系列变量的简洁语法。

基本语法：

- 赋值号左侧的{}用于批量声明变量，右侧对象的属性值将被赋值给左侧的变量
- **对象属性的值将被赋值给与属性名相同的变量**
- 注意解构的变量名不要和外面的变量名冲突，否则会报错
- 对象中找不到与变量名一致的属性时，变量值为undefined

```js
	const {name, age} = {name: '苏白', age: 24};
	console.log(name, age); // 苏白 24
```

- 解构的变量名可以重新命名，通过旧名:新名的方式。

```js
// 对象解构的变量名重命名
	const {name: uname, age: uage} = {name: '苏白', age: 24};
	console.log(uname, uage);
```

- 数组对象解构

```js
// 数组对象解构
const [{name, age}] = [{name: '貂蝉', age: 18}];
console.log(name, age);
```

- 多级对象解构

```js
	const pig = {
		name: '佩奇',
		family: {
			mother: '猪妈妈',
			father: '猪爸爸',
			sister: '乔治'
		},
		age: 6
	}
	// 多级对象解构
	const {
		name,
		family: {
			mother, father, sister
		},
		age
	} = pig;
	console.log(name,mother,father,sister,age);
```

## 四、对象进阶

### 4.1 创建对象的三种方式

注意：创建对象有四种方式，但工厂函数创建对象意义不大，了解就行。

（1）字面量创建对象

```js
const obj={
  name:'渔火'
}
```

（2）new Object创建对象

```js
	// new Object创建对象 （了解）
	const obj1 = new Object({
		name: '渔火'
	})
```

（3）构造函数创建对象。本质上对象都是通过构造函数创建的，只是这里的构造函数特指自定义对象构造函数。

### 4.2 构造函数

构造函数也是一种函数，只是比较特殊，用来初始化对象。

**使用构造函数可以批量生成多个类似的对象**。

构造函数与普通函数的区别：

- 约定函数名使用大驼峰命名法
- 只能由new操作符执行

```js
	// 构造函数
	function CreatePeople(name, age) {
		this.name = name
		this.age = age
	}

	//实例化对象
	const yuHuo = new CreatePeople('渔火', 24)
	const suBai = new CreatePeople('苏白', 24)
	console.log(yuHuo)
	console.log(suBai)
```

一些说明：

- 使用new关键字调用函数的行为称为实例化
- 实例化构造函数时没有参数可以省略()，但习惯带上
- 构造函数内部return返回的值是无效的，所以构造函数内部不需要写return，返回值就是新创建的对象
- new Object()、new Date()等都是实例化构造函数

### 4.3 new实例化执行过程

简单说分为四步：

（1）创建新的空对象

（2）构造函数this指向新对象

（3）执行构造函数代码，修改this，添加新的属性

（4）返回新对象

```js
	// 2、构造函数this指向新对象 mi{}
	// 3. 执行构造函数代码，修改this，添加属性 mi.name='小米'...
	// 4. 返回新对象 mi{name:''小米,price:1999,count:20}
	function Goods(name, price, count) {
		this.name = name
		this.price = price
		this.count = count
	}

	// 1、新建一空对象 {}
	const mi = new Goods('小米', 1999, 20)
```

### 4.4 实例成员和静态成员

实例成员：

- 通过构造函数创建的对象称为实例对象，实例对象中的属性和方法称为实例成员。

- 为构造函数传入参数，动态创建结构相同但值不同的对象。

- 构造函数创建的实例对象彼此独立，互不影响。

静态成员：

- 构造函数的属性和方法就是静态成员
- 一般公共特征的属性或方法会被设置为静态成员
- 静态成员方法中的this指向构造函数本身

```js
	function CreatePeople(name, age) {
		this.name = name
		this.age = age
	}

	// 通过构造函数实例化的对象是实例对象，属性和方法是实例成员
	const yuHuo = new CreatePeople('渔火', 24)
	const suBai = new CreatePeople('苏白', 24)
	console.log(yuHuo,suBai)
	
	// 静态成员是构造函数的属性和方法
	CreatePeople.num=10
	CreatePeople.sayHi=function (){
		console.log('hah')
	}
	console.log(CreatePeople.num)
	CreatePeople.sayHi()
```

## 五、内置构造函数

在JS中最主要有6种数据类型，基本数据类型（字符串、数值、布尔、undefined和null）和引用类型（对象）。

但深入了解，其实字符串、数值、布尔等基本数据类型也有专门的构造函数，称为包装类型。

基本数据类型在底层会被包装成复杂数据类型，不需要手动创建。

所以，JS中几乎所有的数据都是基于构造函数创建。

### 5.1 引用类型

#### 5.1.1 Object

`Object` 是内置的构造函数，用于创建普通对象。

推荐使用字面量方式声明对象，而不是 `Object` 构造函数

Object三个常用的静态方法：

`Object.assign` 静态方法创建新的对象

`Object.keys` 静态方法获取对象中所有属性

`Object.values` 表态方法获取对象中所有属性值

```js
	const obj = {
		name: '渔火',
		age: 24
	}
	// Object三个常用的静态方法
	// 1. Object.keys 获取对象的属性名
	console.log(Object.keys(obj)) // 返回数组

	// 2. Object.values 获取对象的属性值
	console.log(Object.values(obj)) // 返回数组

	// 3. Object.assign(拷贝后的新对象，被拷贝的对象) 拷贝对象
	const yuHou = {}
	Object.assign(yuHou, obj)
	console.log(yuHou)

	//Object.assign经常用于给对象添加属性
	Object.assign(obj, {gender: '男'})
	console.log(obj)
```

#### 5.1.2 Array

`Array` 是内置的构造函数，用于创建数组。

数组赋值后，无论修改哪个变量另一个对象的数据值也会相当发生改变。

1. 推荐使用字面量方式声明数组，而不是 `Array` 构造函数
2. **实例方法 `forEach` 用于遍历数组，替代 `for` 循环** 
3. **实例方法 `filter` 过滤数组单元值，生成新数组**

4. **实例方法 `map` 迭代原数组，生成新数组**

5. **实例方法 `join` 数组元素拼接为字符串，返回字符串**
6. **实例方法  `find`  查找元素， 返回符合测试条件的第一个数组元素值，如果没有符合条件的则返回 undefined**

```js
	const arr = [
		{name: '小米', price: 2999},
		{name: '华为', price: 3999}
	]
	// 查找华为
	const hw=arr.find(item => item.name === '华为')
	console.log(hw)
```

7. **实例方法`every` 检测数组所有元素是否都符合指定条件，如果所有元素都通过检测返回 true，否则返回 false**

```js
const arr = [1, 2, 3, 4, 5, 6, 7, 8]

// 判断数组中是否有满足条件的元素，返回布尔值。只有都满足，才会为true，否则为false
let isTrue = arr.every(item => item >= 5)
	console.log(isTrue)
	
//判断数组中是否有满足条件的元素，返回布尔值。只要有一个满足就为true，全都不满足为false
	isTrue = arr.some(item => item >= 5)
	console.log(isTrue)
```

8. 实例方法`some` 检测数组中的元素是否满足指定条件   **如果数组中有**元素满足条件返回 true，否则返回 false
9. 实例方法 `concat`  合并两个数组，返回生成新数组
10. 实例方法 `sort` 对原数组单元值排序
11. 实例方法 `splice` 删除或替换原数组单元
12. 实例方法 `reverse` 反转数组
13. 实例方法 `findIndex`  查找元素的索引值
14. **实例方法 `reduce` 累计器，返回函数累计处理的结果，经常用于求和**

```js
	/*
	*	reduce:累计器，返回函数处理的结果，经常用于求和
	* 基础语法：arr.reduce(function(累计值，当前元素[,索引号][,源数组]){},起始值)
	* 起始值可以省略，写了就作为第一次累计的开始值
	*
	* 回调函数参数：
	*  （1）累计值
	*       1、有起始值，就以起始值为准，作为累计值。累计值=起始值
	*       2、没有起始值，累计值以数组第一个元素作为起始值，开始累计
	*       3、后面遍历就会用后面的数组元素累计到累计值中，类似求和中的sum
	* */
	const arr = [1, 2, 3, 4, 5, 6]

	// 有起始值
	// let result = arr.reduce(function (prev, item) {
	// console.log(11) // 测试遍历的次数 3
	// 	return prev + item
	// }, 0)

	// 没有起始值
	let result = arr.reduce((prev, item) => {
		console.log(22) // 测试遍历的次数 5
		return prev + item
	})
	console.log(result)
```

15. Array静态方法`Array.from()`将伪数组转为真数组

```js
	const lis = document.querySelectorAll('li')
	// Array.from 将伪数组转为真数组
	const liss = Array.from(lis)
	liss.pop()
	console.log(liss)
```

数组方法很多，一些常用的必须要记住，其他的可以查文档。

### 5.2 包装类型

#### 5.2.1 String

`String` 是内置的构造函数，用于创建字符串。

1. **实例属性 `length` 用来获取字符串的度长**
2. **实例方法 `split('分隔符')` 用来将字符串拆分成数组**
3. **实例方法 `substring（需要截取的第一个字符的索引[,结束的索引号]）` 用于字符串截取**
4. **实例方法 `startsWith(检测字符串[, 检测位置索引号])` 检测是否以某字符开头**
5. **实例方法 `includes(搜索的字符串[, 检测位置索引号])` 判断一个字符串是否包含在另一个字符串中，根据情况返回 true 或 false**
6. 实例方法 `toUpperCase` 用于将字母转换成大写
7. 实例方法 `toLowerCase` 用于将就转换成小写
8. 实例方法 `indexOf`  检测是否包含某字符
9. 实例方法 `endsWith` 检测是否以某字符结尾
10. 实例方法 `replace` 用于替换字符串，支持正则匹配
11. 实例方法 `match` 用于查找字符串，支持正则匹配

注：String 也可以当做普通函数使用，这时它的作用是强制转换成字符串数据类型。

#### 5.2.2 Number

`Number` 是内置的构造函数，用于创建数值。

1. 推荐使用字面量方式声明数值，而不是 `Number` 构造函数
2. 实例方法 `toFixed` 用于设置保留小数位的长度

## 六、面向对象

面向对象编程是一种程序设计思想，它具有3个显著的特征：封装、继承、多态。

### 6.1 封装

封装的本质是将具有关联的代码组合在一起，其优势是能够保证代码复用且易于维护，函数是最典型也是最基础的代码封装形式，面向对象思想中的封装仍以函数为基础，但提供了更高级的封装形式。

#### 命名空间和构造函数封装对比

以普通对象（命名空间）形式封装的代码只是单纯把一系列的变量或函数组合到一起，所有的数据变量都被用来共享（使用 this 访问）。

同样的将变量和函数组合到了一起并能通过 this 实现数据的共享，所不同的是借助构造函数创建出来的实例对象之间是彼此不影响的。

总结：

1. **构造函数体现了面向对象的封装特性**
2. **构造函数实例创建的对象彼此独立、互不影响**
3. 命名空间式的封装无法保证数据的独立性

构造函数存在浪费内存的问题，通过原型能解决这个问题。

#### 原型对象与对象原型

实际上**每一个构造函数都有一个名为 `prototype` 的属性**，译成中文是原型的意思，`prototype` 的是对象类据类型，称为构造函数的原型对象，**每个原型对象都具有 `constructor` 属性**代表了该**原型对象对应的构造函数**。

prototype对象可以挂载函数，对象实例化不会多次创建原型上函数，节约内存。

可以把不变的方法直接定义到prototype对象上，这样所有对象的实例都可以共享这些方法。

**构造函数和原型对象中的this都指向实例化对象。**

JavaScript中对象的工作机制：**当访问对象的属性或方法时，先在当前实例对象是查找，然后再去原型对象查找，并且原型对象被所有实例共享。**

**结合构造函数原型的特征，实际开发重往往会将封装的功能函数添加到原型对象中。**

```js
// 构造函数
// 公共的属性写在构造函数中
function Star(uName, uAge) {
   this.uName = uName
    this.uAge = uAge
}
// 公共的方法挂载到原型prototype
Star.prototype.sing = function () {
   console.log('唱歌')
}
const ldh = new Star('刘德华', 55)
const zxy = new Star('张学友', 58)
ldh.sing()
zxy.sing()
console.log(ldh.sing === zxy.sing) // true 
```

constructor属性的应用场景：指回创建原型对象的构造函数

```js
function Star() {}
// Star.prototype.sing = function () {
//   console.log('唱歌')
// }
// Star.prototype.dance = function () {
//   console.log('跳舞')
// }
console.log(Star.prototype)
Star.prototype = {
 	// 重新指回创造这个原型对象的构造函数
	constructor: Star,
  sing() {
      console.log('唱歌')
  },
  dance() {
    console.log('跳舞')
    }
}
console.log(Star.prototype)
 // const ldh = new Star()
// console.log(Star.prototype.constructor === Star)
```

`prototype`是原型对象，`__proto__`是对象原型。对象都会有一个属性`__proto__`指向构造函数的`prototype`原型对象。这就是对象可以使用构造函数prototype原型对象属性和方法的原因。

注意：

- `__proto__`是JS非标准属性
- [[prototype]]和`__proto__`意义相同，是只读的。
- 用来表明当前实例对象指向哪个原型对象prototype
- `__proto__`对象原型中也有一个constructor属性，指向创建该实例对象的构造函数

<img src="https://pic.imgdb.cn/item/62d5b7c9f54cd3f937b22dd6.png" alt="image-20220719034259002" style="zoom:50%;" />

### 6.2 继承

继承是面向对象编程的另一个特征，通过继承进一步提升代码封装的程度，JavaScript中大多是借助原型对象实现继承的特性。

#### 原型继承

基于构造函数原型对象实现面向对象的继承特性。

```js
      const Preson = {
        eays: 2,
        hesd: 1
      }
      // 继承
      function Woman() {
        // this.eays = 2
        // this.hesd = 1
      }
      // 原型继承
      Woman.prototype = Preson
      // 指回原来的构造函数
      Woman.prototype.constructor = Woman
      function Man() {}
      // Man.prototype = Preson
			// Man.prototype.constructor = Man
      const woman = new Woman()
      const man = new Man()
      console.log(woman)
      console.log(man)
```

原型继承的问题：继承于同一个对象，根据引用类型的特点，都会受影响。解决方法是继承构造函数创建的对象，不要继承一般对象。

改进：

```js
     function Preson() {
        this.eays = 2
        this.hesd = 1
      }
      function Woman() {}
      // 原型继承
      Woman.prototype = new Preson()
      // 指回原来的构造函数
      Woman.prototype.constructor = Woman
      Woman.prototype.baby = function () {
        console.log('baby')
      }
      function Man() {}
      Man.prototype = new Preson()
      const woman = new Woman()
      const man = new Man()
      console.log(woman)
      console.log(man)
```

**所有的对象里面都有`__proto__`对象原型，指向`prptotype`原型对象。**

**所有的原型对象里面都有`constructor`，指向创造原型对象的构造函数。**

#### 原型链

基于原型对象的继承使得不同构造函数的原型对象关联在一起，并且这种关联的关系是一种链状的结构，将原型对象的链状结构关系称为原型链。

在JavaScript对象中包括了一个非标准备的属性 `__proto__` 它指向了构造函数的原型对象，通过它可以清楚的查看原型对象的链状结构。

<img src="https://pic.imgdb.cn/item/62d5c3c0f54cd3f937b9d497.png" alt="image-20220719043407158" style="zoom:50%;" />

原型链查找规则：

- 当访问一个对象的属性（方法）时，首先查找这个对象自身有没有该属性。
- 没有就找它的原型（`__proto__`指向的prototype原型对象）
- 还没有就找原型对象的原型（Object对象）
- 以此类推一直找到Object为止（null）

`__proto__`对象原型的意义在于为对象成员查找机制提供一个方向或者说一条路线。

可以使用instanceof运算符检测构造函数的prptotype属性是否出现在某个实例对象的原型链上。

## 七、深浅拷贝

开发中经常需要复制一个对象。如果直接用赋值会出现修改一个对象的值，其他对象都会修改的问题。这就像生活中内部装修改变了，不管开几扇门，打开里面都一样。

```js
const obj = {
        name: '渔火',
        age: 24
      }
// 直接复制对象，可能会发生一改全改的问题
const o = obj
o.age = 25
console.log(obj) // {name: '渔火', age: 25}
console.log(o) // {name: '渔火', age: 25}
```

### 7.1 浅拷贝

首先浅拷贝和深拷贝只针对引用类型。

浅拷贝:拷贝的是地址

**常见方法:**

拷贝对象:Object.assgin() / 展开运算符 {...obj} 拷贝对象

拷贝数组:Array.prototype.concat() 或者 [...arr]

如果是简单数据类型拷贝值，引用数据类型拷贝的是地址 (简单理解: 如果是单层对象，没问题，如果有多层就有问题)

直接赋值和浅拷贝有什么区别：

- 直接赋值的方法，只要是对象，都会相互影响，因为是直接拷贝对象栈里面的地址
- 浅拷贝如果是一层对象，不相互影响，如果出现多层对象拷贝还会相互影响

```js
const obj = {
        name: '渔火',
        age: 24
      }
      // 展开运算符浅拷贝对象
      const o = { ...obj }
      o.age = 25
      console.log(obj) // {name: '渔火', age: 24}
      console.log(o) // {name: '渔火', age: 25}

      // Object.assign浅拷贝
      const o1={}
      Object.assign(o1,obj)
      o1.age=26
      console.log(o1) // {name: '渔火', age: 26}
      console.log(obj) // {name: '渔火', age: 24}
```

### 7.2 深拷贝

深拷贝:拷贝的是对象，不是地址

**常见方法:** 

1. 通过递归实现深拷贝
1. lodash/cloneDeep
1. 通过JSON.stringify()实现

#### 递归实现深拷贝

函数递归:如果一个函数在内部可以调用其本身，那么这个函数就是递归函数。

简单理解:函数内部自己调用自己, 这个函数就是递归函数

递归函数的作用和循环效果类似

**由于递归很容易发生“栈溢出”错误(stack overflow)，所以必须要加退出条件 return**

案例：通过递归用setTimeout模拟setInterval效果

```js
// setTimeout模拟setInterval
      function getTime() {
        document.querySelector('div').innerHTML = new Date().toLocaleString()
        setTimeout(getTime,1000)
      }
      getTime()
```

递归实现深拷贝：

```js
     const obj = {
        name: '渔火',
        age: 24,
        hobby: ['吃饭', '睡觉', '打豆豆'],
        family: {
          bb: 'bb'
        }
      }
      const o = {}
      // 拷贝函数
      function deepCopy(newObj, oldObj) {
        for (let k in oldObj) {
          // 处理数组
          if (oldObj[k] instanceof Array) {
            newObj[k] = []
            deepCopy(newObj[k], oldObj[k])
          } else if (oldObj[k] instanceof Object) {
            // 处理对象
            newObj[k] = []
            deepCopy(newObj[k], oldObj[k])
          } else {
            newObj[k] = oldObj[k]
          }
        }
      }
      deepCopy(o, obj)
      o.age = 25
      o.hobby[0] = '打游戏'
      o.family.bb = '88'
      console.log(o)
      console.log(obj)
```

简单说明深拷贝过程：

- 深拷贝是做到拷贝出的新对象不会影响旧对象，要实现深拷贝，需要用到递归
- 普通拷贝直接拷贝就行，但遇到数组，再次递归解决数组
- 遇到对象，再次递归解决对象
- 先数组再对象

#### js库lodash里面cloneDeep内部实现深拷贝

```html
<script src="./lodash.min.js"></script>
    <script>
      const obj = {
        name: '渔火',
        age: 24,
        hobby: ['吃饭', '睡觉', '打豆豆'],
        family: {
          bb: 'bb'
        }
      }
      const o = _.cloneDeep(obj)
      o.family.bb = '88'
      console.log(o)
      console.log(obj)
    </script>
```

#### 通过JSON.stringify()实现深拷贝

```js
    const obj = {
        name: '渔火',
        age: 24,
        hobby: ['吃饭', '睡觉', '打豆豆'],
        family: {
          bb: 'bb'
        }
      }
      // 把对象转为字符串,再转为对象
      const o = JSON.parse(JSON.stringify(obj))
      o.family.bb = '88'
      console.log(o)
      console.log(obj)
```

## 八、异常处理

异常处理是指预估代码执行过程中可能发生的错误，然后最大程度的避免错误的发生导致整个程序无法继续运行。

了解程序异常处理的方法，可以提升代码运行的健壮性。

### 8.1 throw抛异常

throw抛出异常信息，程序也会终止执行

throw后面跟的是错误提示信息

Error对象配合throw使用，能够设置更详细的错误信息

```js
function fn(x, y) {
        if (!x || !y) {
          throw new Error('没有参数传递进来')
        }
        return x + y
      }
      fn()
```

### 8.2 try/catch 捕获异常

通过try/catch 捕获错误信息(浏览器提供的错误信息)

将预估可能发生错误的代码写在try代码段中

如果try代码段中出现错误后，会执行catch代码段，并截获到错误信息

finally不管是否有错误，都会执行

```html
   <p>123456</p>
    <script>
      function fn() {
        try {
          // 可能出问题的代码写到try中
          const p = document.querySelector('.p')
          p.style.color = '#f00'
        } catch (err) {
          // cathc中是捕获的异常，只有出问题才执行
          console.log(err.message)
          return
        } finally {
          // finally不管有没有问题都会执行
          alert('执行了')
        }
        console.log(111)
      }
      fn()
    </script>
```

## 九、处理this

### 9.1 this指向

普通函数的调用方式决定了this的值，即【谁调用，this的值指向谁】。

普通函数没有明确调用者时this 值为window，严格模式下没有调用者时this的值为undefined。

箭头函数中的this与普通函数完全不同，也不受调用方式的影响，事实上**箭头函数中并不存在this**!

箭头函数会默认绑定外层this的值，所以在箭头函数中this的值和外层的this是一样的。

箭头函数中的this引用的就是最近作用域中的this。

向外层作用域中，一层一层查找this，直到有this的定义。

注意：

- 在开发中【使用箭头函数前需要考虑函数中this的值】，事件回调函数使用箭头函数时，this为全局的window。因此DOM事件回调函数如果里面需要DOM对象的this，则不推荐使用箭头函数。

- 同样由于箭头函数this的原因，基于原型的面向对象也不推荐采用箭头函数

### 9.2 改变this

JavaScript中还允许指定函数中this的指向，有3个方法可以动态指定普通函数中this的指向：

- call()
- apply()
- bind()

#### call()

使用call方法调用函数，同时指定被调用函数中this的值。

语法：fun.call(thisArg, arg1, arg2, ...)

thisArg:在 fun 函数运行时指定的 this 值

arg1，arg2:传递的其他参数

返回值就是函数的返回值，因为它就是调用函数

call()方法使用很少。

```js
 const obj = {
        name: '渔火'
      }
      function fn(x, y) {
        console.log(this)
        console.log(x + y)
      }
      // call() 调用函数，改变this指向
      fn.call(obj, 1, 2)
```

#### apply()

使用apply方法调用函数，同时指定被调用函数中this的值。

语法：fun.apply(thisArg, [argsArray])

thisArg:在fun函数运行时指定的this值

argsArray:传递的值，必须包含在**数组**里面

返回值就是函数的返回值，因为它就是调用函数

因此apply主要跟数组有关系，比如使用 Math.max() 求数组的最大值。

第一个参数不需要指定时可以传入null。

```js
      const obj = {
        name: '渔火'
      }
      function fn(x, y) {
        console.log(this)
        console.log(x + y)
      }
      // apply() 调用函数，改变this指向
      // 参数1是要窒息的this
      // apply的参数2是数组
      fn.apply(obj, [1, 2])

      // 使用场景
      const max = Math.max.apply(Math, [1, 2, 3])
      console.log(max)
```

call和apply的异同:

- 都是调用函数，都能改变this指向
- 参数不一样，apply传递的必须是数组

#### bind()

bind() 方法不会调用函数。但是能改变函数内部this指向。

语法：fun.bind(thisArg, arg1, arg2, ...)

thisArg:在fun函数运行时指定的this值

arg1，arg2:传递的其他参数

返回由指定的this值和初始化参数改造的**原函数拷贝 (新函数)**

因此当只是想改变this指向，并且不想调用这个函数的时候，可以使用bind，比如改变定时器内部的this指向。

这个方法很常用。

```js
     const obj = {
        name: '渔火'
      }
      function fn() {
        console.log(this)
      }
      // 改变this指向，但不调用函数
      // 返回值是一个函数，但this指向已经改过了
      const fun = fn.bind(obj)
      fun()

 document.querySelector('button').addEventListener('click', function () {
        // 禁用按钮
        this.disabled = true
        setTimeout(
          function () {
            // 修改this，由window改为按钮
            this.disabled = false
          }.bind(this),
          2000
        )
      })
```

## 十、性能优化

### 10.1 节流

节流：指连续触发事件但在n秒内只执行一次函数

典型场景：鼠标移动、滚动条、页面尺寸变化等

简单理解：当前时间 - 起始时间大于指定值，执行功能

```js
 const box = document.querySelector('.box')
      let i = 1
      // 鼠标移动函数
      function mouseMove() {
        box.innerHTML = ++i
      }
      // 节流函数
      function throttle(fn, time) {
        // 起始时间
        let startTime = 0
        return function () {
          // 当前时间
          let now = Date.now()
          // 当前时间减去起始时间大于500，执行函数（节流）
          if (now - startTime >= time) {
            fn()
            // 起始时间 = 当前时间
            startTime = now
          }
        }
      }
      box.addEventListener('mousemove', throttle(mouseMove, 500))
```

### 10.2 防抖

防抖就是触发事件后在n秒内函数只执行一次，如果在n秒内又触发了事件，则会重新计算函数执行时间。

典型场景：输入框输入结束才发送请求

简单说：不断清除定时器，开启定时器

```js
const box = document.querySelector('.box')
      let i = 1
      // 鼠标移动函数
      function mouseMove() {
        box.innerHTML = ++i
      }
      // 防抖函数
      function debounce(fn, t) {
        let timeId
        return function () {
          // 有定时器就清除
          if (timeId) clearTimeout(timeId)
          // 开启定时器
          timeId = setTimeout(() => fn(), t)
        }
      }
      box.addEventListener('mousemove', debounce(mouseMove, 200))
```

### 10.3 lodash节流防抖

```html
		<script src="../js/lodash.min.js"></script>
    <script>
      const box = document.querySelector('.box')
      let i = 1
      // 鼠标移动函数
      function mouseMove() {
        box.innerHTML = ++i
      }
      // 节流
      // box.addEventListener('mousemove', _.throttle(mouseMove, 500))
      // 防抖
      box.addEventListener('mousemove', _.debounce(mouseMove,200))
    </script>
```

### 10.4 节流案例

```html
<div class="container">
      <div class="header">
        <a href="http://pip.itcast.cn">
          <img src="https://pip.itcast.cn/img/logo_v3.29b9ba72.png" alt="" />
        </a>
      </div>
      <div class="video">
        <video src="https://v.itheima.net/LapADhV6.mp4" controls></video>
      </div>
      <div class="elevator">
        <a href="javascript:" data-ref="video">视频介绍</a>
        <a href="javascript:" data-ref="intro">课程简介</a>
        <a href="javascript:" data-ref="outline">评论列表</a>
      </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/lodash@4.17.21/lodash.min.js"></script>
    <script>
      // 获取视频元素
      const video = document.querySelector('video')
      // 音频视频绑定事件要用on事件方式绑定处理函数   节流
      video.ontimeupdate = _.throttle(() => {
        // 获得当前视频的时间，并保存到本地
        localStorage.setItem('currentTime', video.currentTime)
      }, 1000)
      // 打开页面，触发事件，读取本地存储的时间，给当前时间
      video.onloadeddata = () => {
        video.currentTime = localStorage.getItem('currentTime') || 0
      }
    </script>
```

```css
* {
        padding: 0;
        margin: 0;
        box-sizing: border-box;
      }

      .container {
        width: 1200px;
        margin: 0 auto;
      }

      .video video {
        width: 100%;
        padding: 20px 0;
      }

      .elevator {
        position: fixed;
        top: 280px;
        right: 20px;
        z-index: 999;
        background: #fff;
        border: 1px solid #e4e4e4;
        width: 60px;
      }

      .elevator a {
        display: block;
        padding: 10px;
        text-decoration: none;
        text-align: center;
        color: #999;
      }

      .elevator a.active {
        color: #1286ff;
      }

      .outline {
        padding-bottom: 300px;
      }
```

