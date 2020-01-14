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