# Python 基础
## Python 简介
### 语言定义
* 解释性语言/脚本语言
* 面向对象语言
* 交互式语言

### 发展历程
* Python 由其他语言发展而来(C、C++、Unix shell 和其他的脚本语言等)
* Python 2.x和Python 3.x 语法同事存在，但两者有较大语法差别
* Python 2.7 是Python 2.x 最后一个版本

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

### 数据类型判断
* type()
	* 仅判断当前数据的数据类型
* isinstance()
	* 判断当前数据的父类数据类型