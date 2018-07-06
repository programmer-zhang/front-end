### 图示讲解js内存
* Q1:

```
var a = 1;
var b = a;
b = 2;
a = ? 
```

* Q2:

```
var a = { name: 'a' };
var b = a;
b.name = 'b';
a.name = ?
```

* Q3:

```
var a = { name: 'a' };
var b = a;
b = { 'name': 'b' };
a.name = ?
```

* Q4:

```
var a = { name: 'a' };
var b = a;
b = null;
a = ?
```

## 介绍几个概念
### js中的类型
* JavaScript是弱类型语言，有6种类型：number、string、boolean、null、undefined和object
* 其中前六种为简单类型，object为复杂类型
* 两种类型的区别在于内存存储他们的方式
	* 简单类型的声明：内存直接将简单类型的值放在栈内存（stack）中
	* 复杂类型的声明：内存会在栈内存和堆内存（heap）中各自开辟空间，将值放在堆内存中，并将存在堆内存中的地址放入栈内存
* 代码中的赋值语句，如`b=a`在内存中的对应操作为：将a的栈内存的内容，覆盖抄写到b的栈内存上，而堆内存不做改动

### js内存图分析题目
