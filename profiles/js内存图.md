# JS 内存图
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
a.name = ?
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
* Q1 

key|stack|heap
:--:|:--:|:--:
a|1||
b|1=>2||
* 声明语句中，将a的值存放在栈内存中，a赋值给b，b的栈内存中存放为1，b第二次进行赋值，栈内存中b的值覆盖为2，a的值仍为1

-----
* Q2

key|stack|heap
:--:|:--:|:--:
a|ADDR|{ name: 'a'}=>{ name: 'b' }|
b|ADDR|{ name: 'a'}=>{ name: 'b' }|
* a的声明为复杂类型的声明，所以a的值会存放在堆内存中，同时这个值是由栈内存中的地址指向，b=a赋值语句同时将b指向堆内存中的这个值，b第二次赋值，同时修改堆内存中的这个对象，因为a，b两个key 的指向相同，所以a的值同时变为{ name: 'b' }

-----
* Q3

key|stack|heap
:--:|:--:|:--:
a|ADDR|{ name: 'a'}|
b|ADDR|{ name: 'a'}=>{ name: 'a' },{ 'name': 'a' }|
* b的第二次赋值中不是指向同一位置的赋值，所以b指向变为两个地址，指向两个对象，两个对象并不相同，但是a的指向不变

-----
* Q4

key|stack|heap
:--:|:--:|:--:
a|ADDR|{ name: 'a'}|
b|null|{ name: 'a'}=>|
* b的第二次赋值为null，简单类型的赋值，将栈内存中的地址清空，所以b的值变为null，a的值不变

### 答案
1. 1
2. b
3. a
4. a
