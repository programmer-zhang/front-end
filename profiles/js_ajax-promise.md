# 使用 promise 手写一个 Ajax 请求

> 本篇文章使用ChatGPT辅助编写

## 阅读本人您将收获
* 手写 `Ajax` 请求
* 使用 `Promise` 手写 `Ajax` 请求
* 两种方式优缺点

## 一个最基础的 `Ajax` 请求

```
// 创建一个 XMLHttpRequest 对象
var xhr = new XMLHttpRequest();

// 配置请求
xhr.open("GET", "https://jsonplaceholder.typicode.com/posts/1", true);

// 设置请求完成时的回调函数
xhr.onreadystatechange = function () {
  // 检查请求是否完成
  if (xhr.readyState === 4) {
    // 检查 HTTP 响应状态
    if (xhr.status === 200) {
      // 请求成功，处理响应数据
      console.log("成功响应:", xhr.responseText);
    } else {
      // 请求失败，处理错误
      console.error("请求失败，状态码:", xhr.status);
    }
  }
};

// 设置网络错误的回调函数
xhr.onerror = function () {
  console.error("网络错误");
};

// 发送请求
xhr.send();
```

## 使用 `Promise` 手写 `Ajax` 请求

* 下面是一个使用 Promise 手写的简单 AJAX 请求的示例

```
// 步骤 1: 创建一个函数，该函数返回一个 Promise 对象
function makeAjaxRequest(url, method) {
  return new Promise((resolve, reject) => {
    // 步骤 2: 创建一个 XMLHttpRequest 对象
    const xhr = new XMLHttpRequest();

    // 步骤 3: 配置请求
    xhr.open(method, url, true);

    // 步骤 4: 设置请求完成时的回调函数
    xhr.onload = function () {
      // 步骤 5: 检查 HTTP 响应状态
      if (xhr.status === 200) {
        // 请求成功，调用 resolve 并传递响应数据
        resolve(xhr.responseText);
      } else {
        // 请求失败，调用 reject 并传递错误信息
        reject(xhr.statusText);
      }
    };

    // 步骤 6: 设置网络错误的回调函数
    xhr.onerror = function () {
      // 网络错误，调用 reject 并传递错误信息
      reject("网络错误");
    };

    // 步骤 7: 发送请求
    xhr.send();
  });
}

// 步骤 8: 使用 Promise 来进行 AJAX 请求
makeAjaxRequest("https://jsonplaceholder.typicode.com/posts/1", "GET")
  .then((response) => {
    console.log("成功响应:", response);
  })
  .catch((error) => {
    console.error("错误:", error);
  });
```

**代码分解：**

1. 创建一个名为 `makeAjaxRequest` 的函数，它接受 `url` 和 `method` 作为参数，并返回一个 Promise 对象。

2. 在 Promise 构造函数中，创建一个 XMLHttpRequest 对象 `xhr`，用于发起 AJAX 请求。

3. 使用 `xhr.open()` 配置请求，设置请求方法和 URL。

4. 设置 `xhr.onload` 回调函数，当请求完成时触发。

5. 在 `xhr.onload` 回调函数中，检查 HTTP 响应状态。如果状态码为 200，表示请求成功，调用 `resolve` 并传递响应数据 `xhr.responseText`。

6. 设置 `xhr.onerror` 回调函数，处理网络错误情况。

7. 在 `xhr.onerror` 回调函数中，调用 `reject` 并传递错误信息 "网络错误"。

8. 使用 `makeAjaxRequest` 函数发起 AJAX 请求，并通过 `.then()` 处理成功响应和 `.catch()` 处理错误。如果请求成功，将输出响应数据，否则将输出错误信息。

## 优缺点
基础的 `AJAX` 请求和使用 `Promise` 的 `AJAX` 请求各有优点和缺点。下面是它们的主要区别：

**基础的 AJAX 请求：**

**优点：**

1. 相对简单：传统的 `AJAX` 请求使用回调函数，对于简单的异步操作来说，代码相对较简单。

2. 兼容性好：基础的 `AJAX` 请求在各种浏览器中都有很好的兼容性，不需要额外的库或工具。

**缺点：**

1. 回调地狱（`Callback Hell`）：当处理多个嵌套的异步操作时，容易导致回调函数的嵌套，使代码难以理解和维护。

2. 错误处理不方便：错误处理通常需要在每个回调函数中处理，导致错误处理逻辑分散和混乱。

**使用 Promise 的 AJAX 请求：**

**优点：**

1. 更清晰的代码结构：`Promise` 可以使用链式调用，使代码更具可读性，避免了回调地狱问题。

2. 统一的错误处理：`Promise` 提供了 `.catch()` 方法，可以集中处理所有错误，使错误处理更加方便和一致。

3. 支持异步/等待（`async/await`）：与 `Promise` 结合使用的 `async/await` 语法可以进一步提高代码的可读性，使异步代码看起来更像同步代码。

**缺点：**

1. 学习曲线：对于初学者来说，Promise 可能需要一些时间来理解和掌握，尤其是对于复杂的异步场景。

2. 兼容性：`Promise` 不支持一些古老的浏览器，但可以通过使用 `polyfill` 或转译工具来解决。
