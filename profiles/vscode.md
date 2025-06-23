# MAC VSCode 使用指南
> 本文使用 `Deepseek` 辅助编写

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

### **三、前端开发必备插件**
#### 1. **核心工具**
- **ESLint**：实时 JavaScript/TS 代码检查
- **Prettier**：代码自动格式化（配合保存使用）
- **GitLens**：代码行级 Git 历史追溯
- **Auto Close Tag**：自动闭合 HTML/JSX 标签
- **Import Cost**：显示导入包大小

#### 2. **框架增强**
- **Volar** (Vue 3) / **Vetur** (Vue 2)
- **React Refactor**：快速创建组件片段
- **Tailwind CSS IntelliSense**：Tailwind 智能提示

#### 3. **效率工具**
- **Tabnine**：AI 代码补全
- **Live Server**：一键启动本地服务器
- **Error Lens**：行内显示错误信息
- **Code Spell Checker**：单词拼写检查

### **四、高效工作流技巧**
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

### **五、个性化优化建议**
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

### **六、问题排查技巧**
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