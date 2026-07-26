# PM2 使用指南（2026 最新版 · 新手向）

> 适用环境：macOS（MacBook）+ Node.js / Bun  
> 当前最新版本：`pm2@7.0.3`（支持 Node.js ≥18、Bun ≥1）

## 1. PM2 是什么？为什么还要用它？

PM2（Process Manager 2）是 Node.js / Bun 生态里最常用的**生产级进程管理器**。

它解决的核心问题只有一个：

> **让你的 Node 应用在后台一直跑着，挂了自动重启，能充分利用多核，日志可管，重启零停机。**

### 主要能力

| 能力 | 说明 |
|------|------|
| 守护进程 | 崩溃自动重启 |
| 集群模式 | 一键多进程 + 负载均衡（利用全部 CPU） |
| 零停机重载 | `pm2 reload`，不中断请求 |
| 日志管理 | 集中收集、轮转 |
| 开机自启 | 生成 systemd / launchd 脚本 |
| 监控 | 内存、CPU、重启次数实时查看 |
| 多语言 | 不仅 Node，Python、Shell、二进制也能管 |

简单说：开发时用 `node app.js` 或 `tsx watch`，上线就交给 PM2。

---

## 2. 安装（macOS）

```bash
# 推荐用最新版
npm install pm2@latest -g

# 或用 Bun
bun install pm2 -g
```

验证：

```bash
pm2 -v
# 应输出 7.0.x
```

---

## 3. 最常用命令速查

```bash
# 启动
pm2 start app.js                    # 默认 fork 模式
pm2 start app.js -i max             # 集群模式，使用全部 CPU
pm2 start app.js --name my-api      # 指定名字

# 查看
pm2 ls                              # 进程列表
pm2 monit                           # 实时监控面板
pm2 logs                            # 实时日志
pm2 logs my-api --lines 200         # 查看最近 200 行

# 控制
pm2 stop my-api
pm2 restart my-api
pm2 reload my-api                   # 零停机重载（推荐生产用）
pm2 delete my-api

# 保存 & 开机自启（macOS 很重要）
pm2 save
pm2 startup                         # 按提示执行生成的命令
```

---

## 4. 实战示例

### 4.1 最简单启动

```bash
# 假设你有一个 Express 应用
pm2 start server.js --name api
```

### 4.2 集群模式（推荐生产）

```bash
# 自动按 CPU 核心数启动多个实例
pm2 start server.js -i max --name api
```

### 4.3 使用 ecosystem 配置文件（强烈推荐）

创建 `ecosystem.config.js`：

```js
module.exports = {
  apps: [
    {
      name: 'api',
      script: './dist/server.js',
      instances: 'max',          // 或具体数字 4
      exec_mode: 'cluster',
      max_memory_restart: '500M',
      env: {
        NODE_ENV: 'production',
        PORT: 3000
      },
      env_development: {
        NODE_ENV: 'development',
        PORT: 3001
      },
      watch: false,              // 生产环境关掉
      log_date_format: 'YYYY-MM-DD HH:mm:ss',
      error_file: './logs/err.log',
      out_file: './logs/out.log',
      merge_logs: true
    }
  ]
};
```

启动：

```bash
pm2 start ecosystem.config.js --env production
```

之后所有操作都可以用名字：

```bash
pm2 reload api
pm2 logs api
```

### 4.4 开发时开启 watch（热重载）

```bash
pm2 start server.js --name api --watch --ignore-watch="node_modules logs"
```

### 4.5 日志轮转（防止日志把磁盘撑爆）

```bash
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 10M
pm2 set pm2-logrotate:retain 7
```

---

## 5. 生产环境标准流程（macOS / Linux 通用）

```bash
# 1. 安装依赖 & 构建
npm ci
npm run build

# 2. 启动
pm2 start ecosystem.config.js --env production

# 3. 保存当前进程列表
pm2 save

# 4. 设置开机自启（macOS 会生成 launchd）
pm2 startup
# 按提示执行那条 sudo 命令

# 5. 以后更新代码
git pull
npm ci
npm run build
pm2 reload api          # 零停机
pm2 save
```

---

## 6. AI 时代，PM2 还有没有用？

**结论：有，而且结合得越来越深。**

### 6.1 为什么还需要 PM2？

AI 编程工具（Cursor、Claude Code、Codex、Windsurf 等）极大提升了**写代码和调试**的效率，但它们解决的是「开发阶段」的问题。

上线后你仍然需要：

- 进程崩溃自动拉起
- 多核利用
- 日志集中管理
- 开机自启
- 零停机发布

这些是**运行时运维**问题，AI 写代码写得再好，也绕不过去。Docker / K8s 当然可以替代，但对于单机 VPS、中小型项目、个人项目，PM2 仍然是最轻量、最直接的选择。

### 6.2 当前大家怎么把 PM2 和 AI 结合使用？

#### 方式一：让 AI 直接生成 / 维护 ecosystem.config.js

在 Cursor / Claude Code 里直接说：

> 「帮我写一个生产级的 ecosystem.config.js，开启集群模式，内存超过 600M 自动重启，日志按日期分割」

AI 会立刻生成完整配置，你只需 `pm2 start`。

#### 方式二：PM2 + MCP（Model Context Protocol）—— 真正让 AI 管进程

2025 年底开始，社区已经出现了 `pm2-mcp`，把 PM2 的能力暴露成 MCP 工具。Claude Code、Cursor、Codex 可以直接调用：

```bash
# 安装后注册到 Claude Code
claude mcp add pm2-mcp -- pm2-mcp
```

之后你可以在对话里直接说：

- 「列出当前所有 PM2 进程」
- 「把 api 服务重载一下」
- 「查看 api 最近 100 行错误日志」
- 「把内存超过 400M 的进程重启」

AI 不再只是写代码，而是**直接操作运行中的服务**。

#### 方式三：AI 写部署脚本 + PM2 执行

常见工作流：

1. AI 生成完整的 `deploy.sh`（git pull → build → pm2 reload → pm2 save）
2. 你在服务器执行，或者用 GitHub Actions 触发
3. 本地用 Cursor 继续改代码，AI 帮忙维护 ecosystem 和部署脚本

#### 方式四：开发阶段用 AI + watch，生产用 PM2

```bash
# 开发
tsx watch src/index.ts          # 或 nodemon / AI 推荐的热重载

# 生产
pm2 start ecosystem.config.js
```

两者职责分离，非常干净。

---

## 7. 常见坑（新手必看）

1. **忘记 `pm2 save`**  
   重启机器后进程列表会丢。养成 `pm2 reload` 后立刻 `pm2 save` 的习惯。

2. **集群模式 vs Fork 模式**  
   - HTTP 服务 → 用 `cluster` + `-i max`
   - 定时任务、WebSocket 有状态 → 用 `fork`

3. **macOS 开机自启**  
   `pm2 startup` 生成的是 launchd，执行后要用 `pm2 save`。

4. **日志位置**  
   默认在 `~/.pm2/logs/`，可以用 `pm2 logs` 直接看，不需要自己找文件。

5. **更新 PM2 本身**  
   ```bash
   npm install pm2@latest -g
   pm2 update
   ```

---

## 8. 快速 Cheat Sheet

```bash
pm2 start app.js -i max --name api
pm2 reload api
pm2 logs api --lines 100
pm2 monit
pm2 save
pm2 startup
pm2 delete all
pm2 flush                    # 清空所有日志
```

---

## 总结

- PM2 解决的是「进程如何稳定跑在生产环境」的问题，和 AI 写代码并不冲突。
- 2026 年它依然是单机 / 小团队最省心的选择。
- 真正的趋势是：**AI 负责写代码 + 生成配置 + 通过 MCP 直接操控 PM2**，形成闭环。

把 `ecosystem.config.js` 交给 AI 维护，把 `pm2 reload` 交给流水线或 MCP，你只需要关注业务逻辑就行了。