# [JavaScript]前端实用主义  收藏就完了
> 由于之前的前端大佬工作甚是繁重，身体欠恙，急需六味地黄丸和韭菜补充，在头条大佬的美色诱惑下，将由超重的我给大家带来前端知识。

> 本期是我的处女作，一向以活好著称的我给大家来点精华，不搞假大空，直接上实操，不用看懂，收藏就完了，以后你肯定会用到的。

> 今天带来的是前端开发中经常碰到的数字问题，解决方式有些过于粗暴，未来还会不断美化更新。

> 也欢迎大家关注我的Github，共同学习，共同提高。编者不才，如有问题，欢迎雅正，若有收获，请尽情用star羞辱我。另附[Github地址](https://github.com/programmer-zhang)


## 充分利用JavaScript自带原生方法解决问题
> 前端页面和数据处理虽然看起来简单，但是一个优秀的前端工程师对于细节的把控也是至关重要的，如何合理处理业务也体现前端工程师对性能优化的功力(后期我会专门出一期性能优化的长文，敬请期待。)

### 获取数组最大值和最小值
* 利用sort排序方法
	* 针对数组进行排序，数组第一个和最后一个就是最大值和最小值

```
let arr = [1,2,3,4,5,6,7];
arr.sort(function (a, b) {
	 return a-b;
}); 
//sort传入一个排序函数，a-b为升序排序，b-a为降序排序
let min = arr[0]; // 1
let max = arr[arr.length - 1]; //7
```
* 使用Math中的max/min方法
	* 可以使用apply来实现。apply传入的是一个数组

```
let arr = [1,2,3,4,5,6,7];
let max = Math.max.apply(null, arr);
let min = Math.min.apply(null, arr);
console.log(max, min) // 7,1
```

### 浮点数取整
* 丢弃小数部分,保留整数部分
	* `parseInt(7/2)`
* 向上取整,有小数就整数部分加1
	* `Math.ceil(7/2)`
* 四舍五入
	* `Math.round(7/2)`
* 向下取整
	* `Math.floor(7/2)`
* 注：都是JS内置对象

### 数组去重

* ES6

```
arr2=[1,1,1,2,2,2,3,4]
arr1 = Array.from(new Set(arr2)) //arr1=[1,2,3,4]
```
* push( )去重

```
let arr3 = [];  
for(var i = 0; i < arr.length; i++) {  
    (function(i) {  
        if(arr3.indexOf(arr[i]) == -1) { //不包含该值则返回-1  
            arr3.push(arr[i]);  
        }  
    }(i))  
}  
console.log(arr3); 
//如果当前数组的第i项在当前数组中第一次出现的位置不是i，那么表示第i项是重复的，忽略掉。否则存入结果数组  
let arr4 = [arr[0]];  
for(var i = 1; i < arr.length; i++) {  
    (function(i) {  
        if(arr.indexOf(arr[i]) == i) {  
            arr4.push(arr[i]);  
        }  
    }(i))  
}  
console.log(arr4);  
```
* sort()去重

```
let arrSort = arr.sort();  
let arr5 = [];  
for(let i = 0; i< arrSort.length; i++) {  
    if(arrSort[i] != arrSort[i+1]) {  
        arr5.push(arrSort[i]);  
    }  
}  
console.log(arr5);  
```
* splice( )去重

```
for(let i = 0, len = arr6.length; i < len; i++) {  
    for(let j = i + 1; j < len; j++) {  
        if(arr6[i] === arr6[j]) {  
            arr6.splice(i,1);  
            len--;  
            j--;  
        }  
    }  
}  
console.log(arr6); 
```

### 用好 filter，map，every, find和其它 ES6 新增的高阶遍历函数
* 将数组中的false值去掉

```
const array = [3, 4, 5, 2, 3, undefined, null, 0, ""];
const compact = arr => arr.filter(Boolean);
compact(array);
//[3, 4, 5, 2, 3]
```
* 判断用户是否全部是成年人

```
const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 35 }
];
users.every(user => user.age >= 18);
//false
```
* 判断用户中是否有30岁以上的人

```
const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 35 }
];
users.find(item => item.age>30);
//{name: "Will", age: 35}
```

## 合理利用正则表达式解决问题

### `<input>` 标签输入限制
* 文本框只能输入数字代码(小数点也不能输入) 

```
<input onkeyup="this.value=this.value.replace(/\D/g,'')"  
onafterpaste="this.value=this.value.replace(/\D/g,'')"> 
```

* 只能输入数字,能输小数点. 

```
<input onkeyup="if(isNaN(value))execCommand('undo')" onafterpaste="if(isNaN(value))execCommand('undo')"> 
<input name=txt1 onchange="
	if(/\D/.test(this.value)){
	alert('只能输入数字');
	this.value='';}"> 
```

* 数字和小数点方法二 

```
<input type=text tvalue="" ovalue="" onkeypress="
	if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;
		else this.tvalue=this.value;
	if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.ovalue=this.value" 
		onkeyup="if(!this.value.match(/^[\+\-]?\d*?\.?\d*?$/))this.value=this.t_value;
		else this.tvalue=this.value;
	if(this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?)?$/))this.ovalue=this.value" 
		onblur="if(!this.value.match(/^(?:[\+\-]?\d+(?:\.\d+)?|\.\d*?)?$/))this.value=this.o_value;
		else{if(this.value.match(/^\.\d+$/))this.value=0+this.value;
	if(this.value.match(/^\.$/))this.value=0;this.ovalue=this.value}">
```

* 只能输入字母和汉字 

```
<input onkeyup="value=value.replace(/[\d]/g,'') "onbeforepaste="
		clipboardData.setData('text',clipboardData.getData('text').replace(/[\d]/g,''))" 
		maxlength=10 name="Numbers"> 
```

* 只能输入英文字母和数字,不能输入中文

``` 
<input onkeyup="value=value.replace(/[^\w\.\/]/ig,'')"> 
```

* 只能输入数字和英文

```
<font color="Red">chun</font> 
<input onKeyUp="value=value.replace(/[^\d|chun]/g,'')"> 
```

* 小数点后只能有最多两位(数字,中文都可输入),不能输入字母和运算符号: 

```
<input onKeyPress="
	if((event.keyCode<48 || event.keyCode>57) && event.keyCode!=46 || /\.\d\d$/.test(value))
	event.returnValue=false"> 
```
* 小数点后只能有最多两位(数字,字母,中文都可输入),可以输入运算符号:
 
```
<input onkeyup="this.value=this.value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3')"> 
```
*  input的type设置为number后可以输入e

```
<input type='number' onkeypress='return( /[\d]/.test(String.fromCharCode(event.keyCode) ) )' />
```

### JS判断字符串是否全是空格

```
let test = "   \n   ";
if(test.match(/^\s+$/)){
    console.log("all space or \\n")
}
if(test.match(/^[ ]+$/)){
    console.log("all space")
}
if(test.match(/^[ ]*$/)){
    console.log("all space or empty")
}
if(test.match(/^\s*$/)){
    console.log("all space or \\n or empty")
}
```

### 常用正则匹配
* `/^\\d+$/`　　//非负整数（正整数 + 0）
* `/^[0-9]*[1-9][0-9]*$/`　　//正整数
* `/^((-\\d+)|(0+))$/`　　//非正整数（负整数 + 0）
* `/^-[0-9]*[1-9][0-9]*$/`　　//负整数
* `/^-?\\d+$/`　　　　//整数
* `let reg = /^([^0][0-9]+|0)$/;
	if (reg.test(params.xzd2BorrowRate)) {
    	params.xzd2BorrowRate += '.0';
	}`			//整数
* `/^\\d+(\\.\\d+)?$/`　　//非负浮点数（正浮点数 + 0）
* `/^(([0-9]+\\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\\.[0-9]+)|([0-9]*[1-9][0-9]*))$/`　　//正浮点数
* `/^((-\\d+(\\.\\d+)?)|(0+(\\.0+)?))$/`　　//非正浮点数（负浮点数 + 0）
* `/^(-(([0-9]+\\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\\.[0-9]+)|([0-9]*[1-9][0-9]*)))$/`　　//负浮点数
* `/^(-?\\d+)(\\.\\d+)?$/`　　//浮点数
* `replace(/[^0-9]/ig, "")`		//只保留数字

> 今天先暂且给大家带来这些，没什么可读性，但是在工作中经常会使用到，时间匆忙没有好好完善，后续本文会在我的[Github](https://github.com/programmer-zhang)中继续维护，有兴趣可以持续关注。
> 感谢观看，下期再见

### JS扩大checkbox的点击区域
* 试用场景：在表格中点击 td 也能选中 ，或者在父节点也能点击触发事件
* 做法：为 td 元素添加点击事件，在点击事件里面触发checkbox的点击事件：

```
<td onclick="clickTd($(this))">
	<input type="checkbox" name="task"  onclick="oncheckAll(event)">
</td>

function clickTd($t){
    $t.children("input").click();
}
```

* bug浮现：点击checkbox的时候，因为页面元素所在区域重叠，点击事件向上传递，会首先触发checkbox的点击事件，然后触发 td 的点击事件，然后 td 的点击事件会被再次触发，就相当于checkbox点击了两次，所以需要 checkbox 的点击事件执行完之后，让该点击事件停止传递。

* 解决方案：checkbox的点击事件里面加入拦截即可：

```
function oncheckAll(event){
    event.stopPropagation();
}
```

### 计算时间戳间隔时间

```
let date1=new Date();  //开始时间  
let date2=new Date();    //结束时间  
let date3=date2.getTime()-date1.getTime()  //时间差的毫秒数  
------------------------------------------------------------
//计算出相差天数  
var days=Math.floor(date3/(24*3600*1000))   
//计算出小时数   
var leave1=date3%(24*3600*1000)    //计算天数后剩余的毫秒数  
var hours=Math.floor(leave1/(3600*1000))  
//计算相差分钟数  
var leave2=leave1%(3600*1000)        //计算小时数后剩余的毫秒数  
var minutes=Math.floor(leave2/(60*1000))  
//计算相差秒数  
var leave3=leave2%(60*1000)      //计算分钟数后剩余的毫秒数  
var seconds=Math.round(leave3/1000)  
alert(" 相差 "+days+"天 "+hours+"小时 "+minutes+" 分钟"+seconds+" 秒")
```

### JS提取字符串中连续的数字

```
let str = "013d1we22ewfa33rr4rwq0dsf00dsf9fas999";

let getNum = function (Str,isFilter) {
　　　//用来判断是否把连续的0去掉
    isFilter = isFilter || false;
    if (typeof Str === "string") {
        // var arr = Str.match(/(0\d{2,})|([1-9]\d+)/g);
        //"/[1-9]\d{1,}/g",表示匹配1到9,一位数以上的数字(不包括一位数).
        //"/\d{2,}/g",  表示匹配至少二个数字至多无穷位数字
        var arr = Str.match( isFilter ? /[1-9]\d{1,}/g : /\d{2,}/g);
        console.log(arr);
        return arr.map(function (item) {
            //转换为整数，
            //但是提取出来的数字，如果是连续的多个0会被改为一个0，如000---->0，
            //或者0开头的连续非零数字，比如015，会被改为15，这是一个坑
            // return parseInt(item);
            //字符串，连续的多个0也会存在，不会被去掉
            return item;
        });
    } else {
        return [];
    }
}
console.log(getNum(str));//默认不加1，即不会把提取出来的0去掉
```

### JS获取项目根目录

```
function getRootPath(){
    //获取当前网址，如： http://localhost:8083/uimcardprj/share/meun.jsp
    var curWwwPath=window.document.location.href;
    //获取主机地址之后的目录，如： uimcardprj/share/meun.jsp
    var pathName=window.document.location.pathname;
    var pos=curWwwPath.indexOf(pathName);
    //获取主机地址，如： http://localhost:8083
    var localhostPaht=curWwwPath.substring(0,pos);
    //获取带"/"的项目名，如：/uimcardprj
    var projectName=pathName.substring(0,pathName.substr(1).indexOf('/')+1);
    return(localhostPaht+projectName);
}
```
### JS判断小数点后几位
* `num.toString().split(".")[1].length`

### JS截取数字小数点后N位
* 利用`math.round`

```
var original=28.453
// 保留小数点后两位
var result = Math.round(original*100)/100;  //returns 28.45
// 保留小数点后一位
var result = Math.round(original*10)/10;  //returns 28.5
```
* 利用`toFixed`

```
var original=28.453
// 保留小数点后两位
var result=original.toFixed(2); //returns 28.45
// 保留小数点后一位
var result=original.toFixed(1); //returns 28.5
```
