# 什么是webpack
* webpack为模块打包机：
	分析代码结构，找到JavaScript模块以及一些浏览器不能直接运行的拓展语言（scss、sass、Typescript等），并将其转换和打包为合适的格式供浏览器使用。
	
# webpack和Grunt、Gulp的区别
* 没有太多可比性，性质上
	* Gulp和Grunt是优化前端的开发流程的工具
	* webpack是模块化的解决方案，在很多场景下，webpack可以替代Gulp和Grunt
* 工作方式上
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

---
## source-map 开启webpack调试
> 开发离不开调试，方便的调试能提高开发效率，打包后的文件调试时如何定位错误位置，webpack中为我们提供了 source-map 这个方式

* 配置source maps，需要配置devtool，它有以下四种不同的配置选项，各具优缺点

<style> table th:first-of-type { width: 200px; } </style>
dev-tool配置 | 结果 
:-: | :-: 
source-map | 在一个单独文件中产生一个完整且功能完全的文件。这个文件具有最好的source map,但是它会减慢打包速度
cheap-module-source-map | 在一个单独的文件中产生一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号）,会对调试造成不便。
eval-source-map | 使用eval打包源文件模块，在同一个文件中生产干净的完整版的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。在开发阶段这是一个非常好的选项，在生产阶段则一定要不开启这个选项。
cheap-module-eval-source-map | 这是在打包文件时最快的生产source map的方法，生产的 Source map 会和打包后的JavaScript文件同行显示，没有影射列，和eval-source-map选项具有相似的缺点。
* 四种打包方式从上至下打包速度越来越快，但同时较快的打包速度的后果就是对执行和调试有一定的影响。
* 如果大型项目可以使用 `source-map`，如果是中小型项目使用 `eval-source-map` 就完全可以应对，需要强调说明的是，`source map` 只适用于开发阶段，生产环境记得修改这些调试设置。
