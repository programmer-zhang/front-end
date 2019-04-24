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
* 可以通过toString() 来获取每个对象的类型。为了每个对象都能通过 Object.prototype.toString() 来检测，需要以 Function.prototype.call() 或者 Function.prototype.apply() 的形式来调用

```
var toString = Object.prototype.toString;

toString.call(123); //"[object Number]"
toString.call('abcdef'); //"[object String]"
toString.call(true); //"[object Boolean]"
toString.call([1, 2, 3, 4]); //"[object Array]"
toString.call({name:'wenzi', age:25}); //"[object Object]"
toString.call(function(){ console.log('this is function'); }); //"[object Function]"
toString.call(undefined); //"[object Undefined]"
toString.call(null); //"[object Null]"
toString.call(new Date()); //"[object Date]"
toString.call(/^[a-zA-Z]{5,20}$/); //"[object RegExp]"
toString.call(new Error()); //"[object Error]"
```

### 一个获取变量准确类型的函数

```
function getDataType(obj) {
  let type = typeof obj;

  if (type !== 'object') {
    return type;
  }
  //如果不是object类型的数据，直接用typeof就能判断出来

  //如果是object类型数据，准确判断类型必须使用Object.prototype.toString.call(obj)的方式才能判断
  return Object.prototype.toString.call(obj).replace(/^\[object (\S+)\]$/, '$1');
}
```