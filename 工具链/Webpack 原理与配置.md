## Webpack 核心知识点

### 核心概念

| 名词   | 说明                                          |
| ------ | --------------------------------------------- | ------------------ |
| Entry  | 入口文件，默认是 `./src/index.js`             |
| Output | 打包输出配置                                  |
| Loader | 让 webpack 能处理非 JS 文件，如 CSS/TS/Vue 等 |
| Plugin | 扩展功能，如压缩、注入 HTML、拷贝资源         |
| Mode   | `'development'                                | 'production'` 模式 |

### 配置示例

```js
module.exports = {
  mode: "development",
  entry: "./src/index.js",
  output: {
    path: __dirname + "/dist",
    filename: "bundle.js",
  },
  module: {
    rules: [
      { test: /\.css$/, use: ["style-loader", "css-loader"] },
      { test: /\.vue$/, loader: "vue-loader" },
    ],
  },
  plugins: [new HtmlWebpackPlugin({ template: "./index.html" })],
};
```
