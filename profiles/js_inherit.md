> 面向对象语言的三大特征为 继承、封装、多态，如何让 JavaScript 语言实现继承也是面试中的高频面试题

## 阅读本文您将收获
* 实现继承的几种方式
* 实现继承多种方式的优劣

## 继承方式一：原型链继承
* 利用原型链让一个引用类型继承另外一个引用类型的属性和方法

```
function superType() {}
function subType() {}
subType.prototype = new superType()
```

## 继承方式二：构造函数继承

```
function superType() {}
function subType() {
	superType.call(this)
}
let inheritTest = new superType()
```

## 继承方式三：组合继承(寄生组合继承)

```
function superType() {}
function subType() {
	superType.call(this)
}
subType.prototype = new superType()
subType.prototype.constructr = superType()
```

## 继承方式四：extends 实现继承(ES6 方法)
* 原理: 本质上也是原型链继承，实现了两步原型链继承
* 大多数浏览器的 ES5 实现之中，每一个对象都有 `_proto_` 属性，指向对应的构造函数的 `prototype`属性
* Class 作为构造函数的语法糖，同时有 `prototype` 属性和 `_proto_` 属性，因此同时存在两条继承链
	* 子类 `_proto_`属性，表示构造函数的继承，总是指向父类。（把子类构造函数(Child)的原型(`_proto_`)指向了父类构造函数(Parent)）
	* 子类 `prototype` 属性的 `_proto_` 属性，表示方法的继承，总是指向父类的 `prototype` 属性

```
class Zoo {
      constructor(x, y) {
		this.x = x;
		this.y = y;
      }
}
class Dog extends Zoo {
      constructor(x,y,z) {
	      // 子类实例的创建基于父类实例,只有super方法才能得到父类实例
		super(x,y)
		this.z = z;
      }
}
const dog = new Dog(1,2,3)
console.log(dog)
console.log(dog instanceof Dog)  // true
console.log(dog instanceof Zoo)  // true
```