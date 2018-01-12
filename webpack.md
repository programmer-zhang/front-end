# 什么是webpack
* webpack为模块打包机：
	分析代码结构，找到JavaScript模块以及一些浏览器不能直接运行的拓展语言（scss、sass、Typescript等），并将其转换和打包为合适的格式供浏览器使用。
	
# webpack和Grunt、Gulp的区别
* 没有太多可比性，性质上：
	* Gulp和Grunt是优化前端的开发流程的工具
	* webpack是模块化的解决方案，在很多场景下，webpack可以替代Gulp和Grunt
* 工作方式上：
	* Gulp和Grunt：在一个配置文件中，指明对某些文件进行类似编译，组合，压缩等任务的具体步骤，工具之后可以自动替你完成这些任务
	* webpack：把你的项目当做一个整体，通过一个给定的主文件（如index.js），Webpack将从这个文件开始找到你的项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件
	* webpack处理速度更快更直接，能打包更多不同类型的文件

# 为什么要用webpack
* 能完成现有的模块化工具不能完成的任务
	* 将依赖树拆分成按需加载的块
	* 初始化加载的耗时尽量少
	* 各种静态资源都可以视作模块
	* 将第三方库整合成模块的能力
	* 可以自定义打包逻辑的能力
	* 适合大项目，无论是单页还是多页的 Web 应用

# 使用webpack
### 安装
* 使用npm安装新建一个空的联系文件夹，在终端中输入以下命令

		//全局安装
		npm install -g webpack
		//安装到你的项目目录
		npm install --save-dev webpack
		
### 正式使用webpack前的准备
1.	在上述练习文件夹中创建一个package.json文件，这是一个标准的npm说明文件，里面蕴含了丰富的信息，包括当前项目的依赖模块，自定义的脚本任务等等。在终端中使用`npm init`命令可以自动创建这个package.json文件

> 输入这个命令后，终端会问你一系列诸如项目名称，项目描述，作者等信息，不过不用担心，如果你不准备在npm中发布你的模块，这些问题的答案都不重要，回车默认即可。

2.	package.json文件已经就绪，我们在本项目中使用`npm install --save-dev webpack`命令安装Webpack作为依赖包
3.	回到之前的空文件夹，并在里面创建两个文件夹，app文件夹和public文件夹，app文件夹用来存放原始数据和我们将写的JavaScript模块，public文件夹用来存放之后供浏览器读取的文件（包括使用webpack打包生成的js文件以及一个index.html文件）。接下来我们再创建三个文件:
	* `index.html`--放在public文件夹中
	* `Greeter.js`--放在App文件夹中
	* `main.js`--挡在App文件夹中