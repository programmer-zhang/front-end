# NUXT 使用指南

#### 1. 项目引入

首先，确保你已经安装了 Node.js 和 npm。然后，你可以通过以下命令初始化一个新的 NUXT.js 项目：

```bash
npx create-nuxt-app my-nuxt-app
```

根据提示选择你的项目配置，比如选择是否使用服务器端渲染 (SSR)、选择 UI 框架等。初始化完成后，进入项目目录：

```bash
cd my-nuxt-app
```

#### 2. 代码编写

NUXT.js 使用 Vue.js 组件来构建页面。在 `pages` 目录下创建 Vue 文件，NUXT.js 会根据这些文件自动生成路由配置。

例如，在 `pages/index.vue` 中编写一个简单的页面：

```html
<template>
  <div>
    <h1>Welcome to my NUXT.js App!</h1>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello, NUXT.js!',
    };
  },
};
</script>

<style scoped>
h1 {
  color: #41b883;
}
</style>
```

#### 3. 自动路由配置

NUXT.js 会根据 `pages` 目录下的 Vue 文件自动生成路由配置，无需手动配置路由。这简化了路由的管理。

#### 4. 插件配置

NUXT.js 允许你配置插件，可以是第三方库或你自己编写的插件。在 `nuxt.config.js` 文件中配置插件：

```javascript
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/my-plugin.js', // 你的插件路径
  ],
};
```

#### 5. 静态站点生成 (SSG)

如果你需要生成静态站点，可以在 `nuxt.config.js` 中配置：

```javascript
// nuxt.config.js
export default {
  target: 'static',
};
```

然后，运行以下命令生成静态文件：

```bash
npm run generate
```

生成的文件将存储在 `dist` 目录下，你可以将这些文件部署到任何支持静态文件的托管服务上。

#### 6. 运行项目

最后，通过以下命令启动你的 NUXT.js 项目：

```bash
npm run dev
```

访问 http://localhost:3000 即可查看你的应用程序。

以上是一个简单的 NUXT.js 使用指南，涵盖了项目引入、代码编写、自动路由配置、插件配置、静态站点生成等方面。希望这能帮助你入门 NUXT.js，快速构建强大的 Vue.js 应用程序！