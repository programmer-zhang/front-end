# Python 基础
## Python 简介
### 语言定义
* 解释性语言/脚本语言
* 面向对象语言
* 交互式语言

### 发展历程
* `Python` 由其他语言发展而来(C、C++、Unix shell 和其他的脚本语言等)
* `Python 2.x` 和 `Python 3.x` 语法同事存在，但两者有较大语法差别
* `Python 2.7` 是 `Python 2.x` 最后一个版本

### 语法特点
* 关键词较少，结构简单，**易于学习**
* 定义清晰，**易于阅读**
* **易于维护**
* **扩展性强且可嵌入**，Python程序可以调用其他语言编写的关键代码，也可嵌入C/C++程序中

## Python 基础
### 标准数据类型
* 可变数据
	* Number 数字
		* int 整型，Python3 中长整型也是这个类型，Python2中是long
		* bool 布尔 ，Python种布尔是数字类型
		* float 浮点数
		* complex 复数
	* String 字符串
	* Tuple 元组
* 不可变数据
	* List 列表
	* Set 集合
	* Dictionary 字典

### Number 数字类型
##### 数据类型判断
* `type()`
	* 仅判断当前数据的数据类型
* `isinstance()`
	* 判断当前数据的父类数据类型

##### 数值计算
* 基础运算大多与JS相同，只有除法特殊
* 2 / 4 ---> 除法，得到一个浮点数
	* 0.5
* 2 // 4 ---> 除法，得到一个整数
	* 2
* 在混合计算时，Python会把整型转换成浮点数进行计算

##### 删除 Number 类型 对象
* `del numberName`

### String 字符串类型
* 字符串使用单引号 `'` 或双引号 `"` 标识，使用反斜杠 `\` 转义特殊字符
* 索引方式和 Number 类型相同
* 字符串类型属于不可变数据类型，不能改变

##### Python 字符串格式化
* 用法是将一个值插入到一个有字符串格式符 `%s` 的字符串中

```
print('%s 是冠军!' % ('泰山'))
```

##### 常用内置函数
* `find(str, beg=0, end=len(string))` 
	* 查找指定字符串，包含返回开始的索引值，否则返回-1
* `index(str, beg=0, end=len(string))`
	* 与find方法相同，只不过找不到会报异常
* `len(str)`
	* 返回字符串长度
* `join()`
	* 以指定字符串作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
* `replace(old, new [, max])`
	* 把 将字符串中的 old 替换成 new,如果 max 指定，则替换不超过 max 次

### List 列表类型
* 列表类型也支持使用 `list[start:end:length]` 进行数据处理

##### 常用内置函数
* `list.append(obj)`
	* 列表末尾添加新对象
* `list.count(obj)`
	* 统计某个元素在列表中出现的次数
* `list.insert(index, obj)` 
	* 将对象插入列表
* `list.remove(obj)`
	* 移出列表中某个值的第一个匹配项
* `list.reverse()`
	* 列表反向
* `list.sort()`
	* 对列表进行排序

### Tuple 元组类型
* 不可变数据类型
* 写在小括号 `()` 中，元素之间使用逗号隔开
* 也支持使用 `list[start:end:length]` 进行数据处理
* 构造包含0个或1个元素的元组比较特殊
	* `tup1=()` # 空元组
	* `tup2=(10,)` # 一个元素，需要在元素后添加逗号

##### 常用内置函数
* `len()`
	* 计算元组元素个数
* `max()`
	* 返回元组中元素最大值
* `min()`
	* 返回元组中元素最小值
* `tuple()`
	* 将可迭代数据转换成元组

### Set 集合类型
* `parame = {value1,value1,...}` 或 `set(value)`
* 创建空集合必须使用 `set()`,`{ }` 是用来创建空字典的，后面后有介绍
* Set 定义时，重复的元素会被自动去掉
* 可以进行集合运算，包括差集、并集、交集、查找不同时存在的元素

### Dictionary 字典类型
* 与列表的区别在于字典是无序的对象集合，类似于JS中的对象类型，通过 `键` 来进行存取而不是index
* 与JS中的对象类型相似，也有 `keys()`, `values()`等自带方法
* 使用构造函数机型字典类型创建 `dict()`
	* `dict([('name1', 1), ('name2', 2), ('name3', 3)])`
* 创建空字典 `{ }`

## 数据类型转换
### 隐式类型转换
* 与JS种类似，在运算过程中自动进行的转换
* 数字类型进行计算时，结果为转为精度更高的数字类型
* 不同基本数据类型间的运算会报 TypeError，无法完成隐式类型转换

### 显式类型转换
* `int(x, base=10)` 转换为十进制整数
* `float(x)` 转换为浮点数
* `complex(x, 2)` 转换为复数 x + 2j
* `str(x)` 转换为字符串
* `eval(str)` 计算字符串中的有效Python表达式，并返回一个对象
* `tuple(x)` 转换为元组
* `list(x)` 转换为列表
* `set(x)` 转换为集合
* `dict(x)` 创建一个字典

## Python 推导式
* 可以通过遍历的方式从一个数据序列构建另一个新的数据序列的结构体

> 很好理解，下面举几个例子

### 列表推导式

```
# 计算30以内可以被3整除的整数
>>> names = ['Bob','Tom','alice','Jerry','Wendy','Smith']
>>> new_names = [name.upper()for name in names if len(name)>3]
>>> print(new_names)
['ALICE', 'JERRY', 'WENDY', 'SMITH']
```

### 字典推导式

```
listdemo = ['Google','Runoob', 'Taobao']
# 将列表中各字符串值为键，各字符串的长度为值，组成键值对
>>> newdict = {key:len(key) for key in listdemo}
>>> newdict
{'Google': 6, 'Runoob': 6, 'Taobao': 6}
```

### 集合推导式

```
# 计算数字的平方数
>>> setnew = {i**2 for i in (1,2,3)}
>>> setnew
{1, 4, 9}

# 判断不是abc的字母并输出
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'d', 'r'}
>>> type(a)
<class 'set'>
```

## Python 迭代器
* 访问集合元素的一种方式
* 只能往前迭代，不能后退
* 两个基本方法 `iter()` 和 `next()`

```
>>> list=[1,2,3,4]
>>> it = iter(list)    # 创建迭代器对象
>>> print (next(it))   # 输出迭代器的下一个元素
1
>>> print (next(it))
2
>>>
```

* 迭代器对象也可以使用常规for进行遍历

```
#!/usr/bin/python3
 
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
for x in it:
    print (x, end=" ")
```

## Python 生成器
* 生成器是一个返回迭代器的函数，只能用于迭代操作
* 在调用生成器运行的过程中，每次遇到 `yield` 时函数会暂停并保存当前所有的运行信息，返回 `yield` 的值, 并在下一次执行 `next()` 方法时从当前位置继续运行。

## Python 函数
### Python 函数定义规则
* 以 `def` 关键词开头，后接函数标识符名称和圆括号 ()
* 函数内容以冒号 `:` 起始，并且缩进

```
def printHello(first, last):
	print(a, end=" ")
	
printHello()
```

### Python 函数传参
> 与JavaScript类似

* 必需参数
	* 必须以正确的顺序传入函数，调用时的数量必须和声明时的一样
	* 必需参数不传入会出现语法错误
* 关键字参数
	* 关键字参数的传入顺序不影响函数执行
* 默认参数
* 不定长参数
	* 以元组形式传入，使用`*`

	```
	def printinfo( arg1, *vartuple ):
		"打印任何传入的参数"
		print ("输出: ")
		print (arg1)
		print (vartuple)
	 
	# 调用printinfo 函数
	printinfo( 70, 60, 50 )
	>> 输出: 
		70
		(60, 50)
	```

	* 以字典形式导入，使用`**`

	```
	def printinfo( arg1, **vardict ):
		"打印任何传入的参数"
		print ("输出: ")
		print (arg1)
		print (vardict)
	 
	# 调用printinfo 函数
	printinfo(1, a=2,b=3)
	>> 输出: 
		1
		{'a': 2, 'b': 3}
	```
	
## Python 内置函数
### range() 
* 创建一个整数列表，一般用在 `for` 循环中，返回的是一个可迭代对象(类型是对象)，而不是列表类型

```
>>>range(10)        # 从 0 开始到 9
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> range(1, 11)     # 从 1 开始到 10
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> range(0, 30, 5)  # 步长为 5
[0, 5, 10, 15, 20, 25]
>>> range(0, 10, 3)  # 步长为 3
[0, 3, 6, 9]
>>> range(0, -10, -1) # 负数
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> range(0)
[]
>>> range(1, 0)
[]
```