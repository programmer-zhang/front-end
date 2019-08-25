# 一个 webscoket 实例

> 本实例基于一个 Vue-cli 项目

## 创建思路
### 工厂函数抽离
* 将 webscoket 进行封装，以进行实例化

### 同Vue实例下需要创建多个 webscoket 
* 利用 ES6 class 创建
* path 可自定义传入

### webscoket 回调单独处理
* webscoket handler 通过创建实例后挂载
* 挂载函数自定义

### 心跳检测
* 实例添加公用心跳检测

### Ready State 抽离