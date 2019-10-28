# weex基础使用文档

> 浏览[官方文档](https://weex.apache.org/zh/)后阅读此文更佳

## 写在前面
### 文档内容
* 

### 差异化
#### Weex 和 传统Web 的平台差异
* weex 是跨平台解决方案，web 仅是作为一个开发环境
* weex 不存在DOM
	* 没有 `Element` , `Event` 等对象
	* 只有 有限的事件类型 和 `native` 端常用事件类型，如 `Longpress`, `Appear`等
* weex 不存在BOM
	* 没有 `document` , `window` 等对象
	* 提供自身对象获取设备部分数据 `WXEnvironment`
* weex 提供了原生设备调用 API
	* [《clipboard 剪切板》](https://weex.apache.org/zh/docs/modules/clipboard.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《navigator 导航控制》](https://weex.apache.org/zh/docs/modules/navigator.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)
	* [《storage 本地存储 》](https://weex.apache.org/zh/docs/modules/storage.html?spm=a2c7j.-zh-guide-platform-difference.0.0.1fb6400eP0TKTY)

### weex-toolkit和weexpack的区别
* weex-toolkit 初始化的项目是针对开发单个 Weex 页面而设计的，也就是说这样的项目只包括单个页面开发需要的东西，比如前端页面源文件、webpack 配置、npm 脚本等。项目产生的输出就是一个 JS Bundle 文件，可以自由的进行部署。
* weex-pack 是初始化一个完整的 App 工程，包括 Android 和 iOS 的整个 App 起步，前端页面只是其中的一部分。这样的项目最终产出是一个 Android App 和一个 iOS App。

### 必要条件
* 开发环境
	* nodeJS
	* Android 开发环境
	* IOS 开发环境
* 开发工具
	* Sublime Text
	* Android studio
	* Xcode
* 代码管理
	* Github