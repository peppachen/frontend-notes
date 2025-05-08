## Vite 核心知识点

## 核心优势

| 特性       | 描述                            |
| ---------- | ------------------------------- |
| 极速冷启动 | 使用原生 ES 模块，不打包依赖    |
| 即时热更新 | 利用 `esbuild` 和 HMR，极快反馈 |
| 按需构建   | 页面访问到哪构建到哪            |
| 插件体系   | 基于 Rollup 插件系统            |

### 项目结构

```
vite-project/
├─ index.html
├─ vite.config.ts
├─ src/
│  ├─ main.ts
│  ├─ App.vue
│  └─ components/
```

### Vite 配置常用项

```ts
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
  plugins: [vue()],
  server: {
    port: 3000,
    proxy: {
      "/api": "http://localhost:8000",
    },
  },
  build: {
    outDir: "dist",
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ["vue"],
        },
      },
    },
  },
});
```

### 性能优化技巧

- 使用 CDN 加载重依赖（如 Vue、React）
- 图片资源懒加载
- 多页面配置 + 按需打包
- 配合 PWA 提升离线体验
