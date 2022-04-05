# 前端使用 Node 读写 Excel

## 阅读本文您将收获
* 使用 Node 进行 Excel 文件 读取
* 使用 Node 进行 json 数据写入 Excel 文件
* 几种 Excel 读写依赖对比

## 开发环境
* 开发平台
	* MacOS
* 开发环境及IDE
	* VSCode
	* Node.js ([Node.js 官方安装手册](https://www.runoob.com/nodejs/nodejs-install-setup.html))
	* iTerm 2 (iTerm2 安装及配置教程[请点这里](./item2.md))

## 技术
### 依赖包 `node-xlsx`
* npm 地址 [node-xlsx](https://www.npmjs.com/package/node-xlsx)
* Excel 文件解析和生成器
* 依靠 `SheetJS xlsx` 模块来解析/构建 `Excel` 工作表

### 解析
* 支持 Buffer 读取

```
const workSheetsFromBuffer = xlsx.parse(fs.readFileSync(`${__dirname}/myFile.xlsx`));
```
* 支持文件读取

```
const workSheetsFromFile = xlsx.parse(`${__dirname}/myFile.xlsx`);
```

### 构建

```
const data = [
  [1, 2, 3],
  [true, false, null, 'sheetjs'],
  ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'],
  ['baz', null, 'qux'],
];
let buffer = xlsx.build([{name: 'mySheetName', data: data}]); // 生成 buffer
```

## 开发
