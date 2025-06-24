# MAC VSCode 使用指南
> 本文使用 `DeepSeek` 辅助编写

### **一、基础环境配置**
#### 1. **安装与更新**
- 官网下载：[code.visualstudio.com](https://code.visualstudio.com)
- 终端快速启动：安装后运行 `code .` 打开当前目录
- 自动更新：VS Code 默认开启（可在设置中关闭）

#### 2. **基础设置（`⌘ + ,`）**

```json
// settings.json 核心配置
{
  "editor.fontSize": 14,
  "editor.fontFamily": "'Fira Code', 'Menlo', monospace", // 推荐编程字体
  "editor.fontLigatures": true, // 启用连字符 (需安装 Fira Code)
  "editor.tabSize": 2, // 前端项目常用
  "editor.formatOnSave": true, // 保存自动格式化
  "files.autoSave": "afterDelay", // 自动保存
  "workbench.colorTheme": "Default Dark+", // 默认深色主题
  "terminal.integrated.shell.osx": "/bin/zsh", // 使用 zsh
  "emmet.includeLanguages": { // 扩展 Emmet 支持
    "javascript": "javascriptreact",
    "vue-html": "html"
  }
}
```

### **二、日常高频快捷键（Mac 版）**
| **功能**                | **快捷键**               | **说明**                          |
|-------------------------|--------------------------|-----------------------------------|
| 快速文件跳转            | `⌘ + P`                  | 输入文件名模糊搜索                |
| 符号跳转                | `⌘ + Shift + O`          | 跳转到文件内函数/变量             |
| 多光标编辑              | `⌥ + Click`              | 添加多个光标                      |
|                        | `⌥ + ⌘ + ↑/↓`            | 上下添加光标                      |
| 行操作                  | `⌘ + D`                  | 选中相同单词                      |
|                        | `⌥ + ↑/↓`                | 移动当前行                        |
|                        | `⇧ + ⌥ + ↑/↓`            | 复制当前行                        |
| 代码折叠                | `⌘ + K` → `⌘ + [0-9]`    | 折叠指定层级代码                  |
| 终端切换                | `` Ctrl + ` ``           | 打开/关闭终端                     |
| 分屏操作                | `⌘ + \`                  | 垂直分屏                          |
|                        | `⌘ + K` → `⌘ + ←/→`      | 切换分屏组                        |
| 重构                    | `F2`                     | 重命名符号 (跨文件生效)           |
| 快速修复                | `⌘ + .`                  | 显示代码建议 (ESLint/TS 错误修复) |

### **三、高效工作流技巧**
#### 1. **智能编码**
- **Emmet 加速**：
  - HTML 中输入 `div.container>ul.list>li.item*5` → Tab 键生成完整结构
  - CSS 中输入 `m10` → Tab 生成 `margin: 10px;`
- **代码片段**：
  创建自定义 snippets（`⌘ + P` → `>snippets`），例如：
  ```json
  "React FC": {
    "prefix": "rfc",
    "body": [
      "import React from 'react';",
      "",
      "const ${1:Component} = () => {",
      "  return (",
      "    <div>$0</div>",
      "  );",
      "};",
      "",
      "export default ${1:Component};"
    ]
  }
  ```

#### 2. **调试配置**
```json
// .vscode/launch.json (Chrome 调试)
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Launch Chrome",
      "url": "http://localhost:3000",
      "webRoot": "${workspaceFolder}/src"
    }
  ]
}
```
使用 `F5` 启动调试，`F9` 设置断点。

#### 3. **终端集成**
- 多终端：`` Ctrl + ` `` 打开后，按 `⌘ + Shift + 5` 拆分终端
- 任务运行：`⌘ + Shift + B` 运行 npm scripts（需配置 tasks.json）

#### 4. **版本控制**
- `⌘ + Shift + G` 打开 Git 面板
- 代码对比：点击修改的文件 → `Space` 键展开对比
- 提交代码：`⌘ + Enter` 提交暂存区变更

### **四、个性化优化建议**
1. **主题定制**
   - 安装 **Material Icon Theme** 文件图标主题
   - 推荐配色方案：**One Dark Pro** / **GitHub Dark**

2. **键盘映射**
   - 修改不顺手快捷键：`⌘ + K` → `⌘ + S` 打开键盘设置
   - 示例：将 `Format Document` 绑定到 `⌥ + F`

3. **状态栏定制**
   - 右键状态栏 → 隐藏不需要的项（如编码格式、行尾符）

4. **远程开发**
   - 安装 **Remote - SSH** 插件 → 连接远程服务器开发

### **五、问题排查技巧**
1. **性能问题**：
   - 打开命令面板 (`⌘ + Shift + P`) → `>Developer: Show Running Extensions` 查看扩展性能
   - 禁用大型插件（如旧版 Vue 工具）

2. **配置冲突**：
   - 在设置中搜索 `@ext:插件ID` 查看插件专属配置
   - 使用 **Workspace Settings** 区分项目配置

3. **快捷键失效**：
   - 检查与其他软件（如 Spectacle/AltTab）快捷键冲突

**附：推荐工作流**

![](../images/vscode/vscode.png)

通过以上配置和技巧，可大幅提升前端开发效率。建议每季度回顾插件列表，卸载不再使用的扩展保持轻量化。

### **六、插件推荐**

#### **(一)、核心效率工具**
1. **ESLint**  
   - 实时 JavaScript/TS 错误检查和自动修复
   - *必备配置*：`"editor.codeActionsOnSave": { "source.fixAll.eslint": true }`

2. **Prettier - Code formatter**  
   - 自动代码格式化（与 ESLint 配合使用 `eslint-config-prettier` 避免冲突）

3. **GitLens**  
   - 增强 Git 功能：代码作者追溯、提交历史、行级 blame 信息

4. **Error Lens**  
   - **行内显示错误信息**（告别频繁查看底部状态栏）

5. **Tabnine** / **GitHub Copilot**  
   - AI 智能代码补全（大幅减少重复代码编写）

#### **(二)、框架专属插件**
##### Vue 生态
- **Volar** (Vue 3 官方推荐)  
  - 支持 `<script setup>`、TypeScript、模板语法高亮  
  - *替代已废弃的 Vetur*
- **Vue VSCode Snippets**  
  - 快速生成 `v-for`, `v-model` 等模板代码

##### React 生态
- **React Refactor**  
  - 一键提取 JSX 为独立组件
- **ES7+ React/Redux/React-Native snippets**  
  - 快捷指令：`rfc` (函数组件), `rafce` (箭头函数组件)

##### CSS/样式工具
- **Tailwind CSS IntelliSense**  
  - Tailwind 类名自动补全 + 悬停预览
- **CSS Modules**  
  - `*.module.css` 智能提示 + 跳转定义
- **PostCSS Language Support**  
  - 支持嵌套语法等现代 CSS 特性

#### **(三)、开发调试神器**
1. **Auto Close Tag**  
   - 自动闭合 HTML/JSX 标签（输入 `>` 时自动生成 `</tag>`）

2. **Auto Rename Tag**  
   - 修改开始标签时自动重命名闭合标签

3. **Import Cost**  
   - 实时显示 import 的包体积（避免引入巨型依赖）

4. **Live Server**  
   - 一键启动带热更新的本地服务器（右键 HTML 文件即可运行）

5. **REST Client**  
   - 直接在 VS Code 发送 API 请求（替代 Postman）  
   - 创建 `.http` 文件发送请求：
     ```http
     GET https://api.example.com/user
     Authorization: Bearer token
     ```

#### **(四)、视觉与导航增强**
1. **Material Icon Theme**  
   - 直观的文件图标主题（快速识别文件类型）

2. **Bracket Pair Colorizer 2**  
   - 彩色高亮匹配括号（复杂嵌套代码救星）

3. **Highlight Matching Tag**  
   - 高亮显示匹配的 HTML/JSX 标签

4. **Code Spell Checker**  
   - 变量名拼写检查（尤其适合非英语母语者）

#### **(五)、高级工作流工具**
1. **Turbo Console Log**  
   - 快速添加/删除 `console.log`（快捷键 `⌥ + ⌘ + L`）
   - 自动添加变量名注释：

     ```js
     console.log('file.tsx:27 > data', data)
     ```

2. **Change Case**  
   - 快速转换命名风格：`camelCase` ⇄ `PascalCase` ⇄ `snake_case`（快捷键 `⌘ + ⌥ + c`）

3. **Project Manager**  
   - 快速切换项目（支持 Git 仓库分组）

4. **SVG Preview**  
   - 实时预览 SVG 文件 + 导出优化

#### **(六)、新兴技术支持**
| **技术栈**       | **推荐插件**                  | **功能亮点**                     |
|------------------|-----------------------------|--------------------------------|
| GraphQL         | **GraphQL**                | 语法高亮 + 自动补全             |
| WebAssembly     | **Wasm Toolkit**           | .wat 文件支持                  |
| Monorepo        | **Nx Console**             | 可视化管理 Nx 命令             |
| 移动端调试      | **weex-devtool**           | 调试 Weex/Uniapp 应用          |

#### **(七)、插件推荐**
1. **按工作区启用插件**  
   - 右键插件 → `Disable (Workspace)` 避免全局启用不相关插件

2. **定期清理**  

   ```bash
   code --list-extensions | xargs -L 1 echo code --uninstall-extension
   ```
   
   生成卸载脚本，移除闲置插件

3. **同步配置**  
   使用 VS Code 设置同步功能 或 **Settings Sync** 插件跨设备同步

> 💡 **黄金组合建议**：
> ESLint + Prettier + Volar + Tailwind IntelliSense + GitLens + Error Lens 
> 覆盖 90% 前端日常开发场景

#### **避免安装的过时插件**
- ❌ Vetur (Vue 2 项目除外)
- ❌ TSLint (已被 ESLint 取代)
- ❌ Babel JavaScript (官方 JS/TS 插件已足够强大)