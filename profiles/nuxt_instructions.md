# NUXT 使用指南

## 阅读本文您将收获
* NUXT.js的主要特点及优势
* 创建 NUXT.js 项目 
* NUXT 项目相关配置

## 0. NUXT 主要特点及优势

1. **服务端渲染 (SSR):** `NUXT.js` 提供了服务端渲染的能力，这意味着你可以在服务器上预渲染页面，从而提高首屏加载性能，优化搜索引擎爬取等。

2. **自动路由配置:** `NUXT.js` 支持文件系统路由，它会根据 `pages` 目录中的 `Vue` 文件自动生成应用的路由配置。这使得路由的配置变得简单和直观。

3. **插件体系:** `NUXT.js` 具有丰富的插件体系，使得集成第三方库和工具非常容易。你可以使用已有的插件，也可以编写自己的插件来满足项目的需求。

4. **Vuex 集成:** `NUXT.js` 集成了 `Vuex`，一个用于状态管理的 `Vue.js` 应用程序架构。这有助于管理应用的状态，并实现组件之间的通信。

5. **自动优化:** `NUXT.js` 自动处理构建和优化，包括代码拆分、压缩、懒加载等，以提高应用性能。

6. **中间件支持:** `NUXT.js` 允许你使用中间件来处理路由变化前后的逻辑，这对于鉴权、页面过渡等任务非常有用。

7. **静态站点生成 (SSG):** 除了服务端渲染，`NUXT.js` 也支持静态站点生成。这意味着你可以预先生成静态 `HTML` 文件，这对于一些不需要实时数据的场景非常有用。

## 1. 项目引入

```bash
npx create-nuxt-app my-nuxt-app
```

* 根据提示选择你的项目配置，比如选择是否使用服务器端渲染 (SSR)、选择 UI 框架等。初始化完成后，进入项目目录：

```bash
cd my-nuxt-app
```

## 2. 代码编写

* `NUXT.js` 使用 `Vue.js` 组件来构建页面。在 `pages` 目录下创建 `Vue` 文件，`NUXT.js` 会根据这些文件自动生成路由配置。

* 例如，在 `pages/index.vue` 中编写一个简单的页面：

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

## 3. 自动路由配置

* NUXT.js 会根据 `pages` 目录下的 Vue 文件自动生成路由配置，无需手动配置路由。这简化了路由的管理。

## 4. 插件配置

NUXT.js 允许你配置插件，可以是第三方库或你自己编写的插件。在 `nuxt.config.js` 文件中配置插件：

```javascript
// nuxt.config.js
export default {
  plugins: [
    '~/plugins/my-plugin.js', // 你的插件路径
  ],
};
```

## 5. 静态站点生成 (SSG)

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

## 6. 运行项目

最后，通过以下命令启动你的 NUXT.js 项目：

```bash
npm run dev
```

访问 http://localhost:3000 即可查看你的应用程序