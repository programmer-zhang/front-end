# 手写一个 promise

> 当涉及到异步编程时，JavaScript 的 Promise 是一个非常有用的工具。本文将向研发人员介绍如何手写一个 Promise，以便更好地理解它的工作原理。

> 本篇文章使用 ChatGPT 进行辅助编写



## 什么是 Promise？

Promise 是一种表示异步操作的对象，它可以有三种状态：等待（pending）、已解决（resolved）、已拒绝（rejected）。一个 Promise 可以在异步操作完成时，将结果返回给调用方，或者在发生错误时抛出异常。

## 创建一个简单的 Promise

以下是一个手写的 Promise 的示例，它模拟了一个异步操作，延迟一段时间后解决（resolve）并返回一个成功消息。

```javascript
class MyPromise {
  constructor(executor) {
    this.status = 'pending';
    this.value = undefined;
    this.error = undefined;
    
    executor(this.resolve.bind(this), this.reject.bind(this));
  }

  resolve(value) {
    if (this.status === 'pending') {
      this.status = 'resolved';
      this.value = value;
    }
  }

  reject(error) {
    if (this.status === 'pending') {
      this.status = 'rejected';
      this.error = error;
    }
  }

  then(onResolved, onRejected) {
    if (this.status === 'resolved') {
      onResolved(this.value);
    } else if (this.status === 'rejected') {
      onRejected(this.error);
    }
  }
}
```

## 使用手写的 Promise

现在，让我们看看如何使用手写的 Promise。

```javascript
function delay(ms) {
  return new MyPromise((resolve, reject) => {
    setTimeout(() => {
      resolve(`Resolved after ${ms} milliseconds`);
    }, ms);
  });
}

delay(2000)
  .then((result) => {
    console.log(result); // 打印 "Resolved after 2000 milliseconds"
  })
  .catch((error) => {
    console.error(error);
  });
```

在上面的示例中，我们使用 `MyPromise` 类创建了一个 Promise，它在延迟指定的时间后解决，并在解决后执行 `then` 方法中的回调函数。

## 结论

手写一个 Promise 可以帮助你更好地理解 JavaScript 异步编程的核心概念。尽管 JavaScript 提供了内置的 Promise 对象，但了解其内部工作原理对于深入理解和解决异步问题至关重要。希望本文对研发人员有所帮助，使他们更加熟悉 Promise 的工作方式。