# 前端使用 Node 实现读写 Excel 文件

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
* 支持对象数据导入 xlsx 文件

```
const data = [
  [1, 2, 3],
  [true, false, null, 'sheetjs'],
  ['foo', 'bar', new Date('2014-02-19T14:30Z'), '0.3'],
  ['baz', null, 'qux'],
];
let buffer = xlsx.build([{name: 'mySheetName', data: data}]); // 生成 buffer
```

* 支持导出多 sheet 表格文件
* 使用 `node-xlsx` 直接构建结果为 `buffer` 文件, 可以使用 `node` 的 `fs` 模块进行重新导出xlsx文件

```
let buffer = xlsx.build([{name: 'mySheetName', data: data}]); 
fs.writeFile('./export-excel.xlsx', buffer, function (err) {});
```

## 开发
### 引入依赖
* 方式一: `node` 项目引入

```
// package.json
"scripts": {
	"build": "node index.js"
},
"dependencies": {
	"node-xlsx": "^0.21.0"
}
// index.js
const xlsx = require('node-xlsx');
let fs = require('fs'); // node 文件模块，读取文件
let path = require('path'); // 解析文件目录
```

### 根据路径读取表格
> 涉及多文件读取和单文件读取

* 单文件读取

```
const excelSheets = xlsx.parse(`${__dirname}/excel.xlsx`);
```

* 多文件读取

```
// 文件类型正则
const reg = /^(?!~).*\.(xls|xlsx|csv)$/;
let filePath = path.resolve(__dirname + '/excel-dir');
// 读取文件中的所有Excle文件
fs.readdir(filePath, (err, files) => {
	// 确保只读取可以识别的表格文件
	files = files.filter(item => reg.test(item));
	files.forEach(item => {
		let fileName = item;
		// 读取单个文件内容
		const excelSheets = xlsx.parse(__dirname + '/excel-dir' + fileName);
	);
});
```

* 查找对应sheet表

> 适用于多sheet表格文件读取，单sheet表格文件仅有一组数据，无须此种方式查找

```
/**
 * node-xlsx 版本找到目标表格
 *
 * @param {string} excelSheets excel解析出来的JSON数据
 * @param {number} sheetOrderNum sheet表序列号,从0开始,默认-1
 * @param {string} sheetName sheet表名称
 * @description sheetOrderNum sheetName 有一个就行,sheetOrderNum优先
 * @returns {number} 当前sheet结果数据
 */

let findNodeExcleSourceData = ({excelSheets, sheetOrderNum = -1, sheetName}) => {
    let workSheet = []; // 需要处理的数据表

    if (sheetOrderNum >= 0) {
        // 按照sheet表索引进行查找
        workSheet = excelSheets[+sheetOrderNum].data; // 指定sheet表
    }
    else if (sheetName) {
        // 按照sheet表表头名进行查找
        let tmpExlJson = excelSheets.filter(item => {
            return item.name === sheetName
        });
        tmpExlJson.length && (workSheet = tmpExlJson[0].data);
    }
    
    if (!workSheet || (workSheet && !workSheet.length)) {
        console.log('warning: 未找到符合条件的sheet表');
        return [];
    }

    return workSheet;
}

// 读取excel表中第一个sheet数据
let sheetData = findNodeExcleSourceData({excelSheets, sheetOrderNum = 0});
// 读取excel表中sheet名为info数据
let sheetData = findNodeExcleSourceData({excelSheets, sheetName = 'info'});
```

### 导出Excel文件

```
exportExcel(excelData = {name: 'sheet1', data: excelData}, 'test-excel');

/**
 * 输出Excel表格
 *
 * @param {Object} excelData 表信息
 * @param {string} fileName 生成的文件名,默认node-excel-export
 */

function exportExcel(excelData, fileName = 'node-excel-export') {
    let excelBuf = xlsx.build([excelData]);
    fs.writeFileSync(`${__dirname}/dist/${fileName}-${Date.now()}.xlsx`, excelBuf);
}
```

### 执行
* npm 执行: `npm run build`
* 脚本执行: `node index.js`

## 示例
### 使用 node-xlsx 将JSON文件导出为 Excel 表格文件

```
// test.json
{"dataArray":[{"name":"xxx","value":"123","id":"xxx"},{"name":"xxx","value":"234","id":"xxx"},{"name":"xxx","value":"345","id":"xxx"}]}

// index.js
/**
 * @file test-json.js
 * @description 读取json输出excel
 * @author programmer-zhang
 */
const xlsx = require('node-xlsx');
let fs = require('fs');

let jsonFilePath = './json-files/test.json';
let jsonData = JSON.parse(fs.readFileSync(jsonFilePath));

let excelData = [];
// 处理JSON数据
jsonData.dataArray && jsonData.dataArray.length && jsonData.dataArray.forEach(item => {
	excelData.push([item.name, item.value, item.id]);
});

exportExcel(excelData = {name: 'sheet1', data: excelData}, 'test-excel');

/**
 * 输出Excel表格
 *
 * @param {Object} excelData 表信息
 * @param {string} fileName 生成的文件名,默认node-excel-export
 */

function exportExcel(excelData, fileName = 'node-excel-export') {
    let excelBuf = xlsx.build([excelData]);
    fs.writeFileSync(`${__dirname}/dist/${fileName}-${Date.now()}.xlsx`, excelBuf);
}
```