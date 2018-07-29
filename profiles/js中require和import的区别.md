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

