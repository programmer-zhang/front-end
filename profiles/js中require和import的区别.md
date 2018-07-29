### 共同点
* 都是为了js模块化编程使用
* css引入用@import

### ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。
* Require是CommonJS的语法，CommonJS的模块是对象，输入时必须查找对象属性。

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
* ES6模块不是对象，而是通过export命令显示指定输出代码，再通过import输入。

```
import { stat, exists, readFile } from 'fs';
```
* 从fs加载“stat, exists, readFile” 三个方法，其他方法不加载

### Export
* 模块是独立的文件，该文件内部的所有的变量外部都无法获取。如果希望获取某个变量，必须通过export输出。

```
// profile.js
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```
* 或者用更好的方式：用大括号指定要输出的一组变量

```
// profile.js
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```
* 除了输出变量，还可以输出函数或者类（class），

```
export function multiply(x, y) {
  return x * y;
};
```
* 还可以批量输出，同样是要包含在大括号里，也可以用as重命名

```
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```
* export 命令规定的是对外接口，必须与模块内部变量建立一一对应的关系

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
* 报错的写法原因是：没有提供对外的接口，第一种直接输出1，第二种虽然有变量m，但还是直接输出1，导致无法解构
* 同样的，function和class的输出，也必须遵守这样的写法

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
* export定义了模块的对外接口后，其他JS文件就可以通过import来加载这个模块

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
* import后的from 可以指定需要导入模块的路径名，可以是绝对路径，也可以是相对路径， .js路径可以省略，如果只有模块名，不带有路径，需要有配置文件指定。
* 注意，import命令具有提升效果，会提升到整个模块的头部，首先执行。（是在编译阶段执行的）
* 因为import是静态执行的，不能使用表达式和变量，即在运行时才能拿到结果的语法结构（eg. if...else...）

### module
* 除了指定加载某个输出值，还可以用（*）指定一个对象，所有的变量都会加载在这个对象上

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
* 模块整体加载所在的那个对象（上例是circle），应该是可以静态分析的，所以不允许运行时改变

```
import * as circle from './circle';

// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```
### export default
* 之前的例子中，使用import导入时，都需要知道模块中所要加载的变量名或函数名，用户可能不想阅读源码，只想直接使用接口，就可以用export default命令，为模块指定输出

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


