## 变量

### No.1 var、let 和 const 的区别
- var 声明的变量会挂载在 window 上，而 let 和 const 声明的变量则不会
- var 声明变量存在变量提升，let 和 const 不存在变量提升，因此二者都会导致暂存性死区的出现
- let 和 const 声明形成块作用域，即 {}
- 同一作用域下 let 和 const 不能声明同名变量，而 var 则可以
- const 一旦声明必须赋值，不能使用 null 占位，且声明后不能再修改，除非是引用类型数据

### No.2 数据类型
> ECMAScript 定义的数据类型主要包括：String、Number、Boolean、Null、Undefined 和 Object。
- 其中 Undefined、Null、Boolean、Number 都属于基本类型；
- Object、Array 和 Function 则属于引用类型；
- 而 String 有些特殊，可以看成行为与基本类型相似的不可变引用类型（字符串值不可变）。

#### 类型转换：分为强制类型转换和隐式类型转换
1. 强制类型转换主要有：
    - 字符串：String()、toString()
    - 数值型：Number()、parseInt()、parseFloat()
    - 布尔型：Boolean()
2. 隐式类型转换
    - 字符串：str+''
    - 数值型：+num
    - 布尔型：!bool
3. 通过对象的 valueof() 方法可得到变量的原始值

### No.3 赋值
对于基本类型值，赋值、浅拷贝、深拷贝时都是复制基本类型的值给新的变量，之后二个变量之间操作不在相互影响。

对于引用类型值，
- 1、赋值 后二个变量指向同一个地址，一个变量改变时，另一个也同样改变；
- 2、浅拷贝 后得到一个新的变量，这个与之前的已经不是指向同一个变量，改变时不会使原数据中的基本类型一同改变，但会改变会原数据中的引用类型数据；
- 3、深拷贝 后得到的是一个新的变量，它的改变不会影响元数据。

**浅拷贝：**
1. 数组常用的浅拷贝方法有 slice、concat、Array.from()，以及 es6 的析构
2. 对象常用的浅拷贝方法 Object.assign()，es6析构

**深拷贝：**
1. 比较简单粗暴的的做法是使用 JSON.parse(JSON.stringify(obj))；
2. 但是需要注意，undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略
3. 手动实现 - 递归

### No.4 传递

所有的函数参数都是按值传递。也就是说把函数外面的值赋值给函数内部的参数，就和把一个值从一个变量赋值给另一个一样；但对于引用类型来说，它被传递的只是引用的地址而已。
```
function setName(obj) {
	obj.name = 'laowang';
	obj = new Object();
	obj.name = 'Tom';
}
var person = new Object();
setName(person);
console.log(person.name); // laowang
```

### No.5 检测

基本类型用 typeof，引用类型用 instanceof
```
console.log(typeof "ECharts");	// "string"
console.log(typeof 10);         // "number"
console.log(typeof true);       // "boolean"
console.log(typeof null);		// "object"
console.log(typeof undefined);  // "undefined"

var items = [];
var obj = {};
function foo(value){
	return value;
}
console.log(items instanceof Array);		// true;
console.log(obj instanceof Object);			// true;
console.log(reflect instanceof Function);	// true;
```
另外，引用类型还可以使用 constructor 属性及 Object.prototype.toString.call().slice(8, -1) 方式获取对象的数据类型。
此外，对于数组对象，可以使用 Array.isArray() 方法。

### No.6 逻辑运算符
>在 JavaScript 中，&& 和 || 的作用只有一个：即进行布尔值的‘且’和‘或’的运算，当运算到某一个变量就得出最终结果之后，就返回哪个变量。

**因此，**
- a && b，如果 a 是 false，那么 b 不管是 true 还是 false，都返回 false，因此不用判断 b 了，这个时候刚好判断到 a，因此返回 a；
如果 a 是 true，那么就要在判断 b，和刚刚一样，不管 b 是 true 是 false，都返回 b。

- a || b，如果 a 是 true，那么 b 不管是 true 还是 false，都返回 true。因此不用判断 b 了，这个时候刚好判断到 a，因此返回 a；
如果 a 是 false，那么就要判断 b，如果 b 是 true，那么返回 true，如果 b 是 false，返回 false，其实不就是返回 b 了吗？

**注意一点，在 js 中 && 运算符优先级大于 ||。**

### No.7 ES6 promise 相关

#### 问：请简单说一下你对 Promise 的理解？
答：Promise 是一种解决异步编程的方案，其核心思想是将异步操作和回调结果的处理从代码层次上进行分离。说白了，在 new promise() 的时候我们放入异步的代码，在 then() 方法中再对异步的返回结果进行处理，仅此而已。

**Promise 具有以下特点:**
1. Promise 对象的状态和数据不允许被外界(直接)修改，且状态一旦被修改则不可逆
2. resolve/reject 做了两件事
    - a.将结果数据放入到Promise对象中
    - b.遍历执行由then方法注册到回调数组中的所有回调函数
3. then 做了四件事
    - a.返回一个新的 promise 对象，以便在链式调用中能够拿到上一个 promise 对象所保存的数据
    - b.注册回调函数，即将异步数据处理的回调函数存入到一个回调数组中
    - c.执行回调函数
    - d.若该回调函数的返回值是一个 promise 对象，那么将此对象所保存的数据通过 then() 的方式传递给我们上面所创建的 promise 对象
4. Promise 对象的 then() 方法参数 onFulfilled() 和 onRejected() 必须是异步的

#### 问：promise.all() 和 promise.race() 的区别？
答：
- Promise.all() 可以将多个 Promise 实例包装成一个新的 Promise 实例，同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回最先被 reject 失败状态的值。
    - Promse.all() 在处理多个异步处理时非常有用，比如说一个页面上需要等两个或多个 ajax 的数据回来以后才正常显示，在此之前只显示 loading 图标；

- Promise.race() 就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3]) 里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。
    - Promise.race() 可以和定时器绑定，用来测试一些接口的响应速度，分析用户的网络状况之类的。比如将一个请求，和三秒后执行定时器包装成 promise 实例，然后加入 promise.race() 队列中，当请求三秒还未响应时候，可以给用户一些提示，或者是一些其他操作

#### 问：并行与串行？
答：
- Promise 并行的功能主要是依赖 Promise.all() 和 Promise.race()。

- Promise 串行的功能并没有提供，需要我们自己实现
```
* sequence() 顺序执行 Promise，并返回结果
* @param {返回promise的函数集合} promises 
* @param {每一步的回调函数，非异步,可以考虑后期支持} cb 
* @param {附加参数} args 
function sequence(promises, cb, ...args) {
	const p = Promise.resolve(),
		len = promises.length
	if (len <= 0) {
		return p
	}
	let i = 0
	//如果cb不是函数
	if (typeof cb !== 'function') {
		cb = null
		args = [cb, ...args]
	}

	function callBack(...params) {
		return p.then(r => {
			return promises[i](r, ...params)
		}).then(r => {
			++i
			cb && cb(r, i, ...params)
			return i > len - 1 ? Promise.resolve(r) : callBack(...params)
		})
	}

	return callBack(...args)
}
```