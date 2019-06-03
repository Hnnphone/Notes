## 函数

### No.1 手动实现一个 bind 函数
```
if (Function.prototype.bind === undefined) {
	Function.prototype.bind = function() {
		var self = this,						// 保存原函数对象
			context = [].shift.call(arguments),	// 保存需要绑定的 this 上下文对象
			args = [].slice.call(arguments);	// 保存传入的参数
		return function() {						// 返回一个新函数
			self.apply(context, [].concat.call(args, [].slice.call(arguments)));
		}
	}
}
```

#### 问，call() 和 apply() 有什么区别？

### No.2 执行上下文（execute context）EC
- 理解：代码执行的环境
- 时机：代码正式执行之前会进入到执行环境
- 工作：
    1. 创建变量对象
        - 1) 变量
        - 2) 函数及函数的参数
        - 3) 全局：window
        - 4) 局部：抽象的但是确实存在

    2. 确认 this 的指向
        - 1) 全局：this --> window
	    - 2) 局部：this --> 调用其的对象

    3. 创建作用域链 - 父级作用域链 + 当前的变量对象

    4. 扩展：
        ```
        ECObj = {
            // 变量对象: { 变量，函数，函数的形参 },
            // scopeChain: 父级作用域链 + 当前的变量对象,
            // this: { window || 调用其的对象 }
        }
        ```

### No.3 形参与实参

#### 什么是形参？
函数声明和定义时使用的变量称为形参，形参的目的是用来接收调用该函数时传递的值

#### 什么是实参？
函数调用时使用的变量称为实参，实参必须是一个确定的值，以便将这些值传递给形参

#### 回调函数与高阶函数？
同步与异步，两者关注的是 消息通信机制
- 同步，发出一个调用时，在没有得到结果之前，该调用就不返回；
- 异步，调用在发出之后，这个调用就直接返回了，结果不会被立即得到，而是通过状态、通知和回调来处理这个调用。

回调函数：
- 当函数被作为参数传递给另一个函数是，我们则称该函数为回调函数；

高阶函数：
- 高阶函数分为两种情况，一种是参数为一个函数对象，另一种是返回值为一个函数对象

### No.4 作用域

#### 闭包：
理解：什么是闭包？
1. 密闭的容器，类似于 set，map 容器，存储数据的
2. 闭包是一个引用了父环境的对象，存放数据的格式 -> key:value

形成条件：
1. 函数嵌套
2. 内部函数引用外部函数的局部变量

闭包的优点及缺点：
- 优点：延长外部函数局部变量的生命周期
- 缺点：容易造成内存泄漏

注意点，
- 合理的使用闭包
- 用完闭包要及时清除（销毁）

#### 说一说在 JS 中 this 的指向问题：
1. 普通函数调用，在严格模式下绑定到 undefined，否则绑定到全局对象；
2. 对象方法调用，绑定方法的对象；
3. 构造函数调用，绑定到构造函数所返回的对象；
4. call、apply 和 bind，非严格模式下 null 和 undefined 会指向全局对象 window；
5. DOM 事件的回调函数，一般指向绑定事件的 DOM 元素；
6. ES6 中的箭头函数，箭头函数会继承外层函数作用域的 this 绑定，如没有外层函数，则是绑定到全局对象。

#### 声明提前？
指的是 JS 检查装载阶段的声明操作和 JS 执行阶段的赋值操作的次序

### No.5 构造函数

#### 说一下构造函数的执行流程？
1. 创建一个新的对象
2. 将构造函数的作用域赋给新的对象（修改 this 指向）
3. 执行函数中的代码
4. 将新建的对象作为返回值返回

#### 构造函数中的返回值为引用类型或非引用类型有什么区别？
- 若返回值为非引用类型，则忽略返回值，即实际返回实例化的对象；
- 若返回值为引用类型，则不可忽略，即实际返回该引用类型

### No.6 原型
**五条原型原则：**
1. 所有的引用类型（数组，对象，函数）都具有对象特性，即可自由扩展属性（除了 null 以外）
2. 所有的引用类型（数组，对象，函数），都有一个 \__proto__ 属性，其属性值是一个普通的对象（隐式原型）
3. 所有的构造函数，都有一个 prototype 属性，属性值也是一个普通的对象（显式原型）
4. 对象的 \__proto__ 都会指向它构造函数的 prototype
5. 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的原型中寻找

**问：Object.prototype 的原型是什么？**
答：null，因此查到 Object.prototype 时就可以停止查找了。

**请说出以下代码的输出：**
```
Function.prototype.prop = 'a';
Object.prototype.prop = 'b';
console.log(Object.prop);
console.log(new Object().prop);
```

**填空，**
```
Object.__proto__ === Function. + ?
Function.prototype.__proto__ === Object. + ?
```

### No.7 宏任务与微任务
宏任务：setTimeout setInterval requestAnimationFrame
1. 宏任务所处的队列就是宏任务队列
2. 第一个宏任务队列中只有一个任务：执行主线程的 JS 代码
3. 宏任务队列可以有多个

微任务：new Promise().then(回调) process.nextTick
1. 微任务所处的队列就是微任务队列
2. 只有一个微任务队列
3. 在上一个宏任务队列执行完毕后如果有微任务队列就会执行微任务队列中的所有任务

### No.8 谈一下你对浏览器运行机制的理解
- 浏览器是多进程的，在浏览器中打开一个网页相当于新起了一个进程，避免某个标签页崩溃导致整个浏览器 crash
- 内核-渲染进程
    1. 浏览器渲染进程是多线程的，比如说GUI渲染线程、JS引擎线程、事件触发线程、定时触发线程及异步AJAX请求线程
    2. JS引擎线程与GUI渲染线程是互斥
    3. JS引擎线程中的所有同步任务会形成一个执行栈
    4. 事件触发线程维护了一个 EventLoop，它的主要作用就是从事件队列中存取对应的任务。
    5. 宏任务与微任务，参上

### No.9 例题解析：

#### No.1
```
function Foo() {
	getName = function() { alert(1); };
	return this;
}
Foo.getName = function() { alert(2); };
Foo.prototype.getName = function() { alert(3); };
var getName = function() { alert(4); };
function getName() { alert(5); };

// 填写出下列的输出结果
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

#### No.2
```
var a = 0;
var b;
class ClassA extends Array {
	constructor(...options) {
		super(options);
	}
	get len() {
		return a++;
	}
	add(a = null) {
		this.length = ++a;
		return this;
	}
}
var a1 = new ClassA(5);
console.log(a1.len + a1.length);
console.log(a1.len);
a1.len = a1.length = 0;
console.log(a1.length);
console.log(a1.len)
console.log(a1.add(5).len + a1.length);
console.log(a1.add(b).len);
console.log(a1.join('-'))
```