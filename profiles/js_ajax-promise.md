# 使用 promise 手写一个 Ajax 请求

> 本篇文章使用ChatGPT辅助编写

## 阅读本人您将收获
* 手写 `Ajax` 请求
* 使用 `Promise` 手写 `Ajax` 请求

## 使用 `Promise` 手写 `Ajax` 请求

* 下面是一个使用 Promise 手写的简单 AJAX 请求的示例

```javascript
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