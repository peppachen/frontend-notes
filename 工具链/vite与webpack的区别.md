# Vue 中 Vite 与 Webpack 的区别

## 🚀 核心对比

| 维度            | Vite                               | Webpack                             |
| --------------- | ---------------------------------- | ----------------------------------- |
| 构建工具核心    | 原生 ES 模块 + ESBuild（预构建）   | 全部打包为一个或多个 Bundle         |
| 冷启动速度      | 极快（毫秒级）                     | 慢（视项目大小，秒级启动）          |
| 热更新性能      | 快速热更新（只更新变更模块）       | 慢，特别是大型项目                  |
| 开发环境        | 使用原生 ESM，按需加载             | 打包后运行                          |
| 构建产物体积    | 更小，优化较好                     | 相对较大                            |
| 插件生态        | 新兴生态，Vite 官方+社区插件       | 成熟丰富                            |
| 配置方式        | 简洁（vite.config.ts / js）        | 灵活但复杂（webpack.config.js）     |
| 学习成本        | 更低                               | 较高（尤其是 Loader / Plugin 配置） |
| 支持 Vue 版本   | Vue 2（需插件）/ Vue 3（官方支持） | Vue 2/3 都可                        |
| TypeScript 支持 | 默认良好支持                       | 需额外配置（ts-loader）             |
| 适用场景        | 小型/中型项目、组件库、快速原型    | 中大型企业项目，构建逻辑复杂场景    |

## 🔍 工作原理差异

### ✅ Vite 的开发模式

- 使用浏览器原生 ES 模块（ESM）加载机制
- 未修改的 `.ts/.vue` 文件不需要重新构建
- 通过 **ESBuild 预构建依赖**，开发体验极快

### ✅ Webpack 的开发模式

- 一次性将所有代码进行打包
- 改动一处就可能重新编译整个模块树
- 热更新性能受限，开发时构建慢

## 🔧 配置对比

### 📦 Webpack 示例

```js
// webpack.config.js
module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "bundle.js",
  },
  module: {
    rules: [
      { test: /\.vue$/, loader: "vue-loader" },
      { test: /\.css$/, use: ["style-loader", "css-loader"] },
    ],
  },
  plugins: [new VueLoaderPlugin()],
};
```

### ⚡️ Vite 示例

```ts
// vite.config.ts
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
  plugins: [vue()],
});
```
