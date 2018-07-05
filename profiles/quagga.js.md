### 欣赏一波官方介绍
* 完全由JavaScript编写
* 支持实时的本地化
* 支持各种类型条形码
* 通过getUserMedia直接访问用户的相机流
* H5与web都可使用

> 注： getUserMedia在大多数浏览器中访问需要安全的来源，这意味着http://只能使用localhost。所有其他主机名需要通过提供https协议进行使用

### 安装和使用
* 官方推荐使用依赖管理工具进行安装
`npm install quagga`
* 在项目中引入<br>
 `import Quagga from 'quagga'; // ES6`
 `const Quagga = require('quagga').default; // Common JS (important: default)`
* 初始化quagga

```
Quagga.init({
    inputStream : {
      name : "Live",
      type : "LiveStream",
      target: document.querySelector('#yourElement')
    },
    decoder : {
      readers : ["code_128_reader"]
    }
  }, function(err) {
      if (err) {
          console.log(err);
          return
      }
      console.log("Initialization finished. Ready to start");
      Quagga.start();
  });
```

* 输出结果

```

Quagga.onDetected(function(data){
    if (data && data.codeResult) {
       console.log(data.codeResult.code);
       return ;
       }
    })
    
```
* 可以通过打印data查看扫码结果

### 各种回调函数
* Quagga.start（）
	* 初始化库时，该方法启动视频流并开始定位和解码图像。