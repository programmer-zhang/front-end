# 前端使用 Node 批量下载网络图片

## 阅读本文您将收获
* 使用 Node 模块进行自动化图片下载
* 使用 Node 模块进行批量图片下载

## 准备工作
* 先拿到单个图片或一堆图片的下载链接
* 如果是有规律的图片链接，那么恭喜你，很简单就可以实现全部图片的下载，如果是拿到的接口response ，需要对JSON数据进行处理后进行批量下载
* 文中涉及的图片批量下载例子为 NFT Azuki 的图片集

```
imgList = [
	'https://ikzttp.mypinata.cloud/ipfs/QmYDvPAXtiJg7s8JdRBSLWdgSphQdac8j1YuQNNxcGE1hg/1.png',
	'https://ikzttp.mypinata.cloud/ipfs/QmYDvPAXtiJg7s8JdRBSLWdgSphQdac8j1YuQNNxcGE1hg/2.png',
	'https://ikzttp.mypinata.cloud/ipfs/QmYDvPAXtiJg7s8JdRBSLWdgSphQdac8j1YuQNNxcGE1hg/3.png',
	'https://ikzttp.mypinata.cloud/ipfs/QmYDvPAXtiJg7s8JdRBSLWdgSphQdac8j1YuQNNxcGE1hg/4.png',
	'https://ikzttp.mypinata.cloud/ipfs/QmYDvPAXtiJg7s8JdRBSLWdgSphQdac8j1YuQNNxcGE1hg/5.png'
	]
```

## 引入模块

> Node 中有很多下载图片的解决方案，可以引入 npm 包来解决问题，如果图片链接简单且数量不大，只需要使用 `request` 依赖包就可以，但依赖包目前已弃用，也可以用 `axios` 来代替即可，毕竟只需要一个可以用来发送请求的依赖就好。

```
"dependencies": {
	"axios": "^0.26.1",
	"request": "^2.88.2" 
  }
```

## 下载图片过程
* 创建文件写入流

```
let writeStream = fs.createWriteStream(dest);
```

* 发送请求并进行存储写入

```
let readStream = request(src);
readStream.pipe(writeStream);
```

## 监听下载状态
> 下载过程中受制于网络环境或对方反扒措施，部分图片可能会出现下载失败的情况，这个时候监听下载状态就显得尤为重要。

> 整个过程利用node文件写入流的回调函数进行完成，根据错误情况输出错误信息从而进行后续的重新下载。

* 配置相关监听

```
readStream.on('end', () => {
    console.log('文件下载成功:' + imgIndex);
});
readStream.on('error', () => {
    console.log('错误信息:' + imgIndex);
});
writeStream.on('finish', () => {
    // console.log('文件写入成功:' + imgIndex);
    writeStream.end();
});
```

## 检查下载情况
* 图片资源的下载情况检查我习惯用图片体积大小进行判断，依然利用node进行体积大小的判断，从而筛选出未成功下载的图片，然后批量进行重新下载。

```
/**
 * 查找有问题的图片
 *
 * @param {string} dirName 文件夹名称
 */
let findErrImg = (dirName = '/dist/azuki-images/') => {
    let filePath = path.resolve(__dirname + dirName);
    fs.readdir(filePath, (err, files) => {
        files.forEach(item => {
            fs.stat(__dirname + dirName + item, (err, data) => {
                if (err) {
                    console.log('出错啦:', item);
                }
                else {
                    if (data.size < minSize) {
                        console.log(item); // 输出文件大小不符正常文件的文件名
                    }
                }
            });
        });
    });
};
```

## 最后附完整代码