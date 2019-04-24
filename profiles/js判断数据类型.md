### typeof
* typeof 返回值对应表

| 类型| 结果 |
| ------ | ------ | ------ |
| String	| "string" |
| Number	| "number" |
| Boolean	| "boolean" |
| Undefined | "undefined" |
| Object、Array、RegExp、null、Date、Error | "object" |
| function | "function" |
| Symbol(ES6新增) | "symbol" |

### instanceof
* instanceof运算符需要指定一个构造函数，或者说指定一个特定的类型，它用来判断这个构造函数的原型是否在给定对象的原型链上

```
123 instanceof Number, //false
'dsfsf' instanceof String, //false
false instanceof Boolean, //false
[1,2,3] instanceof Array, //true
{a:1,b:2,c:3} instanceof Object, //true
function(){console.log('aaa');} instanceof Function, //true
undefined instanceof Object, //false
null instanceof Object, //false
new Date() instanceof Date, //true
/^[a-zA-Z]{5,20}$/ instanceof RegExp, //true
new Error() instanceof Error //true
```
* Number，String，Boolean没有检测出他们的类型，但是如果使用下面的写法则可以检测出来：

```
var num = new Number(123);
var str = new String('dsfsf');
var boolean = new Boolean(false);
```
* 还需要注意`null`和`undefined`都返回了false，这是因为它们的类型就是自己本身，并不是`Object`创建出来它们，所以返回了false。

### constructor
* constructor是prototype对象上的属性，指向构造函数

### toString()