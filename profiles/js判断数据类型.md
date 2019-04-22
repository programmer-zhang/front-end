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