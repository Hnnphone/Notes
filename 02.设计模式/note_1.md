## 设计模式

### No.1 单例模式 - 保证一个类只有一个实例，并提供一个访问它的全局访问点

#### 简单实现
```
function Singleton1() {
	if (typeof Singleton1.instance === 'object') {
		return Singleton1.instance; // 返回构造函数的属性
	}
	Singleton1.instance = this;
}
```

#### 使用 ES 5 实现(IIFE)
```
var Singleton2 = (function() {
	// 实例容器
	var instance;
	// 闭包内声明一个构造函数
	function Singleton() {}
	return {
		// 提供获取单例的方法
		getInstance: function(args) {
			if (instance === undefined) {
				instance = new Singleton();
			}
			return instance;
		}
	}
})()
```

#### 使用 ES 6 实现
```
class Singleton3 {
	constructor() {}
	static getInstance() {
		if (!this.instance) {
			this.instance = new Singleton3();
		}
		return this.instance;
	}
}
```

### No.2 继承

```
// 定义一个动物类
function Animal(name) {
	this.refs = {};
	this.name = name || 'Animal'; // 属性
	this.sleep = function() { // 实例方法
		console.log(this.name + ' 正在睡觉！');
	}
}
// 原型方法
Animal.prototype.eat = function(food) {
	console.log(this.name + ' 正在吃：' + food);
};
```

#### 方式一：原型链继承 - 多实例共享 引用类型属性 的状态，牵一发而动全身
```
function Cat() {};
Cat.prototype = new Animal();
```

#### 方式二：构造继承 - 无法继承原型属性/方法
```
function Dog(name) {
	Animal.call(this);
	this.name = name || 'dog';
}
```

#### 方式三：实例继承 - 无法实现多继承
```
function Pig(name) {
	var instance = new Animal();
	instance.name = name || 'pig';
	return instance;
}
```

#### 方式四：拷贝继承 - 效率较低，内存占用高
```
function Cow(name) {
	var animal = new Animal();
	for (var p in animal) {
		Cow.prototype[p] = animal[p];
	}
	Cow.prototype.name = name || 'cow';
}
```

#### 方式五：组合继承
```
function Bat(name) {
	Animal.call(this);
	this.name = name || 'bat';
}
Bat.prototype = new Animal();
```

### No.3 MVVM

```
// <input id="input" />
var data = {};
var inputs = document.getElementsByTagName('input');
// 数据劫持
Object.defineProperty(data, 'text', {
	set: function(newValue) {
		Array.prototype.slice.call(inputs, 0).forEach(function(input) {
			input.value = newValue;
		})
	}
});
// 事件监听
Array.prototype.slice.call(inputs, 0).forEach(function(input) {
	input.oninput = function(ev) {
		data.text = ev.target.value;
	}
})
```