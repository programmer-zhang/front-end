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
* 在项目中引入

```
import Quagga from 'quagga'; // ES6`
const Quagga = require('quagga').default; // Common JS (important: default)
```

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
* Quagga.stop（）
	* 如果解码器当前正在运行，则在调用stop()之后不再处理任何图像。此外，如果在初始化时请求摄像机流，此操作也会断开摄像机的连接。
* Quagga.onProcessed（回调）
	* 此方法注册callback(data)在处理完成后为每个帧调用的函数。该data对象包含有关操作成功/失败的详细信息。输出根据检测和/或解码是否成功而变化。
* Quagga.onDetected（回调）
	* 注册一个callback(data)功能，只要条形码模式被定位并成功解码就会触发该功能。传递的data对象包含关于解码过程的信息，包括条码结果信息data.codeResult.code
* Quagga.decodeSingle（配置，回调）
	* 与上述调用相反，该方法不依赖于 getUserMedia单个图像并对其进行操作。提供的回调与in中的回调相同，onDetected并包含结果data对象。

* Quagga.offProcessed（handler）
	* 如果onProcessed事件不再相关，则从事件队列中offProcessed删除给定handler事件。

* Quagga.offDetected（handler）
	* 如果onDetected事件不再相关，则从事件队列中offDetected删除给定handler事件。

### 相关扩展
* quagga中的条码是通过init()中的解码器进行配置的，官方提供了很多的解码器，常用的解码形式还是`code_128_reader`
* 官方文档[GitHub地址](https://github.com/serratus/quaggaJS)
