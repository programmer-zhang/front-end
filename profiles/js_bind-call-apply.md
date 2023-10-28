# 手写 bind、call、apply 函数

> 在 JavaScript 中，`bind`, `call`, 和 `apply` 是三种用于控制函数上下文（`this` 关键字）和参数传递的重要方法。这些方法允许你在调用函数时明确指定函数运行时的上下文以及传递参数。在本文中，我们将学习如何手写这三个函数的实现。

> 本篇文章使用 ChatGPT 辅助编写

## 阅读本文您将收获
* `bind`, `call`, 和 `apply` 三种函数的作用
* `bind`, `call`, 和 `apply` 三种函数的使用场景
* `bind`, `call`, 和 `apply` 三种函数的手撸方式

## 1.  `bind` 函数

### 作用
* `bind` 方法用于创建一个新的函数，该函数在调用时具有指定的上下文，同时也可以传递参数。以下是一个手写 `bind` 函数的示例：

### 使用场景
* **绑定回调函数**：事件处理函数的绑定，确保函数在事件触发时具有正确的上下文。

```javascript
// 示例：绑定事件处理函数
const button = document.getElementById('myButton');
const obj = {
  message: 'Button clicked!',
  handleClick: function () {
    console.log(this.message);
  }
};
button.addEventListener('click', obj.handleClick.bind(obj));
```

* **创建偏函数**： 使用 bind 来预定义部分函数参数，以创建特定用途的函数。

```javascript
// 示例：创建具有预定义参数的回调函数
function greet(greeting, name) {
  console.log(`${greeting}, ${name}`);
}

const greetHello = greet.bind(null, 'Hello');
greetHello('Alice'); // 输出：Hello, Alice
```

### 手写一个 `bind` 函数

```javascript
Function.prototype.myBind = function (context, ...args) {
  const originalFunction = this;
  return function (...innerArgs) {
    return originalFunction.apply(context, args.concat(innerArgs));
  };
};

const person = {
  name: "John"
};

function greet(greeting) {
  console.log(`${greeting}, ${this.name}`);
}

const greetPerson = greet.myBind(person, "Hello");
greetPerson(); 
// 输出：Hello, John
```

* 在上述示例中，`myBind` 方法接受一个上下文 `context` 和任意数量的参数 `args`，然后返回一个新的函数，该函数将指定的上下文和参数传递给原始函数。

## 2.  `call` 函数

### 作用
* `call` 方法用于立即调用一个函数，并指定函数的上下文以及参数。

### 使用场景
* **继承与超类调用**： 在对象的构造函数中，可以使用 call 来调用超类构造函数，实现继承。

```javascript
function Animal(name) {
  this.name = name;
}

function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
```

* **数组操作**： 在操作数组时，可以使用 call 来将类数组对象（如 arguments）转换为真正的数组。

```javascript
function sum() {
  const argsArray = Array.prototype.slice.call(arguments);
  return argsArray.reduce((acc, val) => acc + val, 0);
}

console.log(sum(1, 2, 3)); // 输出：6
```

### 手写一个 `call` 函数

```javascript
Function.prototype.myCall = function (context, ...args) {
  return this.apply(context, args);
};

const person = {
  name: "Alice"
};

function introduce(age) {
  console.log(`My name is ${this.name} and I am ${age} years old.`);
}

introduce.myCall(person, 30); 
// 输出：My name is Alice and I am 30 years old.
```

* 在上述示例中，`myCall` 方法接受一个上下文 `context` 和任意数量的参数 `args`，然后使用 `apply` 方法来调用原始函数，并传递上下文和参数。

## 3.  `apply` 函数

* `apply` 方法用于立即调用一个函数，但与 `call` 不同，它接受参数的形式为数组。以下是一个手写 `apply` 函数的示例：

```javascript
Function.prototype.myApply = function (context, args) {
  return this.call(context, ...args);
};

const person = {
  name: "Bob"
};

function saySomething(words) {
  console.log(`${this.name} says: ${words}`);
}

saySomething.myApply(person, ["Hello, World!"]); 
// 输出：Bob says: Hello, World!
```

* 在上述示例中，`myApply` 方法接受一个上下文 `context` 和一个参数数组 `args`，然后使用 `call` 方法来调用原始函数，并展开参数数组以传递给原始函数。

## 结论

手写 `bind`, `call`, 和 `apply` 函数可以帮助你更好地理解这些方法的工作原理。这些方法是 `JavaScript` 中非常有用的工具，用于控制函数的执行上下文和参数传递。通过了解它们的内部实现，你可以更好地掌握它们的用法和灵活性。

希望本文中的示例有助于你深入了解这些方法，并在日常编程中更好地运用它们。通过手写这些函数，你可以更好地理解 JavaScript 函数调用的背后原理，以及如何更好地控制函数的行为。
