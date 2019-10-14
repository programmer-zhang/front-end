# weex使用文档

### weex-toolkit和weexpack的区别
weex-toolkit 初始化的项目是针对开发单个 Weex 页面而设计的，也就是说这样的项目只包括单个页面开发需要的东西，比如前端页面源文件、webpack 配置、npm 脚本等。项目产生的输出就是一个 JS Bundle 文件，可以自由的进行部署。
weex-pack 是初始化一个完整的 App 工程，包括 Android 和 iOS 的整个 App 起步，前端页面只是其中的一部分。这样的项目最终产出是一个 Android App 和一个 iOS App。