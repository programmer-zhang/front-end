### typeof
* typeof 返回值对应表

| 类型| 结果 |
| ------ | ------ |
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
* `constructor` 是 `prototype` 对象上的属性，指向构造函数
* 根据实例对象寻找属性的顺序，若实例对象上没有实例属性或方法时，就去原型链上寻找，因此，实例对象也是能使用 `constructor` 属性的。

```
var num  = 123;
var str  = 'abcdef';
var bool = true;
var arr  = [1, 2, 3, 4];
var json = {name:'wenzi', age:25};
var func = function(){ console.log('this is function'); }
var und  = undefined;
var nul  = null;
var date = new Date();
var reg  = /^[a-zA-Z]{5,20}$/;
var error= new Error();

function Person(){}
var tom = new Person();

// undefined和null没有constructor属性
console.log(
    tom.constructor==Person,
    num.constructor==Number,
    str.constructor==String,
    bool.constructor==Boolean,
    arr.constructor==Array,
    json.constructor==Object,
    func.constructor==Function,
    date.constructor==Date,
    reg.constructor==RegExp,
    error.constructor==Error
);
//所有结果均为true
```

### toString()
* 可以通过`toString()` 来获取每个对象的类型。
* 为了每个对象都能通过 `Object.prototype.toString()` 来检测，需要以 `Function.prototype.call()` 或者 `Function.prototype.apply()` 的形式来调用

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
