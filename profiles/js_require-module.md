# JS 模块化编程

> 随着前端技术的逐渐发展，模块化的概念越来越成熟。

> 随着ES6的出现，模块的设计思想变得尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

## 阅读本文您将收获
* 模块化的概念
* 模块化的演变过程
* 多种模块化资源引入方案详解
* 各种模块化引入方案的常见问题解答

## 模块化需要满足我们哪些需要
* 职责单一
	* 应用复杂需要职责单一的代码组合
* 可替换/可维护性
	* 需要长期更新维护的应用支持可替换
* 可复用性
	* 节省开发时间
	* 代码质量提高
* 按需加载/性能要求
	* 空间上: 只加载当前页面的模块
	* 时间上: 只有当用户表现出需要某一功能意图时才去加载相关模块
* 多人协作的需要
	* 大型应用多人协作，只有功能单一，接口明确，高内聚低耦合的代码才敢放心大胆的修改和使用

## 模块化的理论基础
### 面向对象
* 继承
	* 把不应该暴露给其他代码的逻辑，方法或属性隐藏在模块内部，对外只提供必要接口，进而保证了代码内部的高内聚和低耦合
* 封装、多态

### 设计模式
> 设计模式要解决的是如何组织和协调不同对象去解决程序问题

* 单例模式
	* 单例模式是指在程序运行期间保证一个类只有一个实例。
	* 由于JavaScript没有类的概念，应用单例模式其实就是保证我们的程序在运行期间，某一功能对象一直只是同一个对象，而不是每次都重复创建。
* 订阅者模式
	* 通过addEventListener或attachEvent为dom节点添加事件监听，其实就是在应用订阅者模式。
	* 订阅者模式通过让一组对象去监听一个对象的事件，实现对象间一对多的通信，在最大程度上降低了对象间的耦合度。
* 外观模式
	* 外观模式通过将一个或一组对象的接口封装起来，对外只提供其他代码需要的接口，实现降低代码耦合度。

## 模块化的概念
* 模块就是将一个复杂的程序依据一定的规则（规范）封装成几个块（文件），并进行组合在一起，块的内部数据和实现是私有的，只是向外部暴露一些接口（方法）与外部其它模块通信。
* 一个模块的组成由两部分组成： 数据（内部的属性）、操作数据的行为（内部的函数）

## 模块化的早期实现
### 全局 `Function` 模式
* 早期的这种全局 `Function` 模式比较接近于为了抽离组件化而组件化，各个模块间容易产生污染。

* ModuleFirst.js

```
// 内部数据
let data = '模块1'
// 操作数据的函数
function fun() {
	console.log(`fun() ${data}`);
}
function funNext() {
	console.log(`funNext() ${data}`);
}
```

* ModuleSecond.js

```
let data = '模块2'
function fun() {
	console.log(`fun() ${data2}`);
}
```

* index.html

```
<script type="text/javascript" src="ModuleFirst.js"></script>
<script type="text/javascript" src="ModuleSecond.js"></script>
<script type="text/javascript">
	let data = "修改后的数据";
	fun(); // 冲突
	funNext();
</script>
```

* 全局函数模式是早期的模块化思想之一，这种方式最大的问题在于同时引入的模块会造成数据污染和命名冲突。

### `namespace` 模式/对象写法

* ModuleFirst.js

```
let moduleFirst = new Object({
	data: 'ModuleFirst',
	fun() {
		console.log(`fun() ${this.data}`);
	},
  	funNext() {
		console.log(`funNext() ${this.data}`);
	}
})
```

* ModuleSecond.js

```
let moduleSecond = new Object({
	data: 'ModuleSecond',
	fun() {
		console.log(`fun() ${this.data}`);
	},
  	funNext() {
		console.log(`funNext() ${this.data}`);
	}
})
```

* index.html

```
<script type="text/javascript" src="ModuleFirst.js"></script>
<script type="text/javascript" src="ModuleSecond.js"></script>
<script type="text/javascript">
	// ModuleFirst.js模块
  	moduleFirst.fun()
  	moduleFirst.funNext()
	// ModuleSecond.js模块
 	moduleSecond.fun()
 	moduleSecond.funNext()
  
  	moduleFirst.data = 'other data' //能直接修改模块内部的数据
  	moduleFirst.foo()
</script>
```

* 这种模式是简单的对象封装，虽然解决了命名冲突和数据污染的问题，但是在引用页面还可以直接针对内部数据进行修改。

### 立即执行函数写法

* module.js

```
var module1 = (function(){
	var _count = 0;
	var m1 = function(){
		//...
	};
	var m2 = function(){
		//...
	};
	return {
		m1 : m1,
		m2 : m2
	};
})();
```

* index.html

```
console.info(module1._count); //undefined
```

* 这种方式不太常用，虽然可以达到不暴露私有成员的目的，但是外部代码无法读取模块内部变量

> 下面几种方式是对上面方式的加工，但是不常用，虽然能够解决一些问题，但是都存在着使用上的缺点，以下几种方式有兴趣可以自行了解下

### 放大模式
### 宽放大模式
### 输入全局变量方式

## 模块化规范
### CommonJS
* node.js 诞生，为 `JavaScript` 模块化编程提供了契机，浏览器环境下，网页程序复杂性没有模块不是特别大的问题，但是服务器端一定要有模块能够与操作系统和其他应用程序互动。
* `Cmmon.js` 中的全局方法 `require()` 可以用于进行模块化加载。
* 对node来说，模块存放在本地硬盘，同步加载等待时间就是硬盘的读取时间，这个时间非常短。
* 但是对于浏览器环境编程，存在一个问题，`require()` 的返回是同步的, 意味着有多个依赖的话需要一个一个依次下载，堵塞js脚本的执行。

### AMD 规范
* 异步方式加载模块，模块的加载不影响后续语句的执行
* 依赖这个模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行

```
reuire([module], callback)
```

## 模块化的多种实践方式
### CommonJS require()
* `require()` 是 `CommonJS` 的语法，`CommonJS` 的模块是对象，输入时必须查找对象属性。

```
// CommonJS模块
let { stat, exists, readFile } = require('fs');

// 等同于
let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```
* 整体加载fs模块（即加载fs所有方法），生成一个对象"_fs"，然后再从这个对象上读取三个方法，这叫“运行时加载”，因为只有运行时才能得到这个对象，不能在编译时做到静态化。

### Export
* 模块是独立的文件，该文件内部的所有的变量外部都无法获取。如果希望获取某个变量，必须通过export输出。

```
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```
* 或者用更好的方式：用大括号指定要输出的一组变量。

```
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```
* 除了输出变量，还可以输出函数或者类（class）。

```
export function multiply(x, y) {
  return x * y;
};
```
* 还可以批量输出，同样是要包含在大括号里，也可以用as重命名。

```
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```
* export 命令规定的是对外接口，必须与模块内部变量建立一一对应的关系。

```
// 写法一
export var m = 1;
// 写法二
var m = 1;
export {m};
// 写法三
var n = 1;
export {n as m};
// 报错
export 1;
// 报错
var m = 1;
export m;
```
* 报错的写法原因是：没有提供对外的接口，第一种直接输出1，第二种虽然有变量m，但还是直接输出1，导致无法解构。
* 同样的，function和class的输出，也必须遵守这样的写法。

```
// 报错
function f() {}
export f;
// 正确
export function f() {};
// 正确
function f() {}
export {f};
```
* export语句输出的接口，都是和其对应的值是动态绑定的关系，即通过该接口取到的都是模块内部实时的值。
* export模块可以位于模块中的任何位置，但是必须是在模块顶层，如果在其他作用域内，会报错。

```
function foo() {
  export default 'bar' // SyntaxError
}
foo()
```

### Import
* export定义了模块的对外接口后，其他JS文件就可以通过import来加载这个模块。

```
// main.js
import {firstName, lastName, year} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```
* import命令接受一对大括号，里面指定要从其他模块导入的变量名，必须与被导入模块（profile.js）对外接口的名称相同。
* 如果想重新给导入的变量一个名字，可以用as关键字

```
import { lastName as surname } from './profile';
```
* `import` 时 `from` 可以指定需要导入模块的路径名，可以是绝对路径，也可以是相对路径， `.js` 路径可以省略，如果只有模块名，不带有路径，需要有配置文件指定。
* 注意，`import` 命令具有提升效果，会提升到整个模块的头部，首先执行。（是在编译阶段执行的）
* 因为 `import` 是静态执行的，不能使用表达式和变量，即在运行时才能拿到结果的语法结构（eg. if...else...）

### module
* 除了指定加载某个输出值，还可以用（*）指定一个对象，所有的变量都会加载在这个对象上。

```
// circle.js。输出两个函数
export function area(radius) {
  return Math.PI * radius * radius;
}
export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// main.js 加载在个模块
import { area, circumference } from './circle';
console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));

//上面写法是逐一指定要加载的方法，整体加载的写法如下。
import * as circle from './circle';
console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```
* 模块整体加载所在的那个对象（上例是circle），应该是可以静态分析的，所以不允许运行时改变。

```
import * as circle from './circle';

// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```
### export default
* 之前的例子中，使用import导入时，都需要知道模块中所要加载的变量名或函数名，用户可能不想阅读源码，只想直接使用接口，就可以用export default命令，为模块指定输出。

```
// export-default.js
export default function () {
  console.log('foo');
}
```
* 其他模块加载该模块时，import命令可以为该匿名函数指定任意名字。

```
// import-default.js
import customName from './export-default';
customName(); // 'foo'
export default也可以用于非匿名函数前。
```
* 下面比较一下默认输出和正常输出。

```
// 第一组
export default function crc32() { // 输出
  // ...
}
import crc32 from 'crc32'; // 输入
// 第二组
export function crc32() { // 输出
  // ...
};
import {crc32} from 'crc32'; // 输入
```
* 可以看出，使用export default时，import语句不用使用大括号。
* import和export命令只能在模块的顶层，不能在代码块之中。否则会语法报错。
* 这样的设计，可以提高编译器效率，但是没有办法实现运行时加载。
* 因为require是运行时加载，所以import命令没有办法代替require的动态加载功能。所以引入了import()函数。完成动态加载。

```
import(specifier)
```
* specifier用来指定所要加载的模块的位置。import能接受什么参数，import()可以接受同样的参数。
* import()返回一个Promise对象。

```
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
	    module.loadPageInto(main);
  })
  .catch(err => {
	    main.textContent = err.message;
  });
```

### import()
* 按需加载
	* import模块在事件监听函数中，只有用户点击了按钮，才会加载这个模块。

```
button.addEventListener('click', event => {
  import('./dialogBox.js')
  .then(dialogBox => {
	    dialogBox.open();
  })
  .catch(error => {
	    /* Error handling */
  })
});
```
* 条件加载
	* import()可以放在if...else语句中，实现条件加载。

```
if (condition) {
  import('moduleA').then(...);
} else {
  import('moduleB').then(...);
}
```


