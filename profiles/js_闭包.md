# 详解 JavaScript 闭包

> 本篇文章使用ChatGPT辅助写作

## 阅读本文您将收获
* 什么是闭包
* 闭包的结构
* 闭包的应用
* 面试中经常遇到的闭包实例题目

**深入理解JavaScript闭包**

JavaScript是一种广泛用于网页开发的脚本语言，其强大的功能使得它成为了前端工程师的首选。在JavaScript中，闭包是一个重要的概念，它可以帮助我们更好地组织和管理代码。本文将详细解释什么是闭包，以及通过代码示例演示闭包在前端开发中的实际应用。

## 什么是闭包

闭包是JavaScript中一个强大且有趣的概念。简而言之，闭包是一个函数，它可以访问其词法作用域之外的变量。换句话说，闭包可以“捕获”在其定义时可访问的变量，即使这个函数在其他地方被调用，它仍然可以使用这些变量。这种能力使得闭包成为处理数据封装、模块化以及创建特定任务的函数的理想选择。

## 闭包的结构

在 JavaScript 中，闭包的结构通常涉及到一个内部函数以及外部函数。内部函数定义在外部函数的内部，并且内部函数可以访问外部函数的变量。当外部函数返回内部函数时，内部函数仍然可以访问外部函数的变量，这就形成了闭包。

## 闭包的应用

以下是一些闭包在前端开发中常见应用的示例：

* **数据封装：**

```javascript
function createCounter() {
  let count = 0;

  function increment() {
    count++;
    console.log(count);
  }

  return increment;
}

const counter = createCounter();
counter(); // 输出: 1
counter(); // 输出: 2
```

在这个例子中，`createCounter`函数返回了一个内部函数`increment`，而这个内部函数可以访问外部函数`createCounter`的`count`变量。每次调用`counter`函数，都会增加并输出计数值。

* **事件处理：**

```javascript
function createButton() {
  const button = document.createElement('button');
  button.textContent = 'Click me';

  button.addEventListener('click', function() {
    console.log('Button clicked');
  });

  return button;
}

const button = createButton();
document.body.appendChild(button);
```

在这个例子中，`createButton`函数创建了一个按钮元素，并为按钮添加了一个点击事件处理函数。即使事件处理函数在将来被触发，它仍然可以访问`button`元素，因为它被定义在了闭包中。

* **模块化开发：**

```javascript
function createCalculator() {
  let result = 0;

  function add(value) {
    result += value;
  }

  function subtract(value) {
    result -= value;
  }

  function getResult() {
    return result;
  }

  return {
    add,
    subtract,
    getResult
  };
}

const calculator = createCalculator();
calculator.add(5);
calculator.subtract(3);
console.log(calculator.getResult()); // 输出: 2
```

在这个例子中，`createCalculator`函数返回了一个包含多个方法的对象，这些方法可以修改和获取`result`变量。这种方式可以实现模块化的开发，每个模块都拥有自己的状态和方法，而不会干扰其他模块。

## 闭包相关高频面试题目

### 题目 1: 什么是闭包？请通过代码示例来解释。

**答案:**
闭包是指一个函数能够访问其定义时所在的词法作用域之外的变量。在JavaScript中，当一个内部函数在外部函数内部被定义，并且内部函数引用了外部函数的变量，那么这个内部函数就形成了一个闭包。

```javascript
function outer() {
  const outerVariable = 'I am from outer scope';

  function inner() {
    console.log(outerVariable);
  }

  return inner;
}

const closureFunction = outer();
closureFunction(); // 输出: "I am from outer scope"
```

### 题目 2: 闭包可能导致什么问题？如何避免这些问题？

**答案:**
闭包可能导致内存泄漏问题，因为闭包会持有对外部变量的引用，导致这些变量无法被垃圾回收。为避免内存泄漏，可以在不需要闭包时手动解除对外部变量的引用，或者在闭包的作用域结束后，确保不再需要访问外部变量。

```javascript
function createLeak() {
  const data = [1, 2, 3];
  return function() {
    console.log(data);
  };
}

const leakyFunction = createLeak();
leakyFunction(); // 将持有对"data"的引用，可能导致内存泄漏
```

### 题目 3: 如何在循环中正确使用闭包？请给出一个示例。

**答案:**
在循环中使用闭包时，需要特别小心，因为闭包捕获的是变量的引用，而不是值。在循环中创建闭包时，通常需要使用立即执行函数表达式（IIFE）来捕获每次迭代的变量。

```javascript
for (var i = 0; i < 5; i++) {
  (function(index) {
    setTimeout(function() {
      console.log(index);
    }, 1000);
  })(i);
}
```

### 题目 4: 如何创建一个私有变量？请使用闭包来实现。

**答案:**
通过闭包可以创建私有变量，这些变量在外部是不可访问的。可以在函数内部定义变量，并返回一个内部函数来操作这个变量。

```javascript
function createCounter() {
  let count = 0;

  return {
    increment: function() {
      count++;
    },
    getCount: function() {
      return count;
    }
  };
}

const counter = createCounter();
counter.increment();
counter.increment();
console.log(counter.getCount()); // 输出: 2
```

### 题目 5: 什么是循环中的事件委托？它如何与闭包相关？

**答案:**
循环中的事件委托是指将事件监听器附加到一个共同的父元素，以便在子元素触发事件时捕获并处理该事件。这在处理大量子元素的情况下可以提高性能。闭包在这里与循环中的事件委托相关，因为通过闭包可以在事件处理函数中访问循环中的索引或其他变量。

```javascript
const buttons = document.querySelectorAll('.button');
for (let i = 0; i < buttons.length; i++) {
  buttons[i].addEventListener('click', function() {
    console.log('Button ' + i + ' clicked');
  });
}
```

### 题目 6: 以下哪个函数是闭包？

```
// 1
let countClicks = 0;
button.addEventListener('click', function clickHandler() {
  countClicks++;
});
// 2
const result = (function immediate(number) {
  const message = `number is: ${number}`;
  return message;
})(100);
// 3
setTimeout(function delayedReload() {
  location.reload();
}, 1000); 
```

**答案:**

判断是否是闭包的简单规则就是，一个函数是否能访问外部函数的变量

* 1、clickHandler函数是闭包，因为它能访问外部的countCLicks。
* 2、immediate函数不是闭包，因为它没有访问到外部的任何一个变量。
* 3、delayedReload函数是闭包，因为它访问到全局变量location，也就是最顶层的函数域。

### 题目 7: 以下代码打印出来的是什么？

```
(function immediateA(a) {
  return (function immediateB(b) {
    console.log(a); // 打印出什么
  })(1);
})(0);
```

**答案:**

打印出 0

* 因为immediateA函数的参数是0，因此传输给a为0。然后immediateB又是在immediateA的函数里，而且它是一个闭包的，所以immediateB里的a能访问到外面immediateA的a，所以打印出 0

### 题目 8：以下代码打印出来的是什么？

```
let count = 0;
(function immediate() {
  if (count === 0) {
    let count = 1;
    console.log(count); // What is logged?
  }
  console.log(count); // What is logged?
})();
```

**答案:**


打印出 1 和 0

* 因为一开头声明了count = 0，然后在immediaye函数是一个闭包，因为它的count能访问到一开头声明的那个count，所以此时count是0，然后在条件块上，因为满足count===0的条件，所以进入条件块里，然后因为let具有块级作用域，所以用let声明count时，此时的count为1，所以第一个console.log(count）打印出1
* 第二个console.log(count)因为是在immediate函数里，而count是会访问到外部的count，也就是一开头声明的那个count，所以为0。

### 题目 9：以下代码打印出来的是什么？

```
for (var i = 0; i < 3; i++) {
  setTimeout(function log() {
    console.log(i); // What is logged?
  }, 1000);
}
```

**答案:**


打印出 3,3,3

* 该代码块执行有两个阶段：
	* 阶段一：
	* 1、for循环有3次，每次循环时都会创建一个新的log函数，而log函数里的setTimeout()是在1000ms后开始执行的。
	* 2、循环完成后，i就变成3，而setTimeout还没开始执行的。
	* 阶段二：
	* 第二个就是发生在1000ms后：
	* 1、setTimeou()就开始执行的，因为是闭包的，所以里面的i能访问到外部的i，而外部的i此时就是3，所以打印出3，之后的setTimeou也是如此的。

### 题目 10：以下代码打印出来的是什么？

```
function createIncrement() {
  let count = 0;
  function increment() { 
    count++;
  }

  let message = `Count is ${count}`;
  function log() {
    console.log(message);
  }
  
  return [increment, log];
}

const [increment, log] = createIncrement();
increment(); 
increment(); 
increment(); 
log(); // What is logged?
```

**答案:**


打印出 Count is 0

* increment被调用3次，每次count都+1，3次后就成为3。
message变量是在createIncrement函数内，它的初始化是“count is 0"。然而，即使count增加1，message始终保持“count is 0"
log函数是一个闭包，它能访问到外部的message，所以打印出“count is 0"








