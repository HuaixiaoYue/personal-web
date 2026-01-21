# Astro 博客：基于Astro生成的个人网站

```sh
npm create astro@latest -- --template blog
```

功能特性：

- ✅ 极简样式（可以自由定制！）
- ✅ 100/100 Lighthouse 性能评分
- ✅ SEO 友好，支持规范 URL 和 OpenGraph 数据
- ✅ 站点地图支持
- ✅ RSS 订阅源支持
- ✅ Markdown 和 MDX 支持

## 🚀 项目结构

在 Astro 项目中，你会看到以下文件夹和文件：

```text
├── public/
├── src/
│   ├── components/
│   ├── content/
│   ├── layouts/
│   └── pages/
├── astro.config.mjs
├── README.md
├── package.json
└── tsconfig.json
```
*   **`src/pages/`**：**路由中心**。
    *   这里的每个 `.astro` 或 `.md` 文件都对应一个网页路径。
    *   修改 `index.astro` 就是修改首页。
*   **`src/content/`**：**内容仓库**。
    *   通常你的博客文章（`.md` 或 `.mdx` 文件）都放在这里。这是你写稿的主要地方。
*   **`src/components/`**：**零件箱**。
    *   存放导航栏 (Nav)、页脚 (Footer)、卡片 (Card) 等重复使用的组件。
*   **`public/`**：**静态资源**。
    *   放你的头像、网站图标 (favicon)、不需要处理的原始图片。通过 `/filename` 即可直接访问。
*   **`src/styles/`**（如果有）：**样式表**。
    *   存放全局 CSS 或 Tailwind 的配置文件。

### ⚙️ 配置文件（偶尔修改）
*   **`astro.config.mjs`**：Astro 的“大脑”。如果你要添加新功能（比如搜索、站点地图），需要在这里配置。
*   **`package.json`**：项目的“清单”。记录了你用 npm 安装了哪些插件，以及 `npm run dev` 等快捷命令。

### 🛠️ 无需手动修改（由系统管理）
*   **`node_modules/`**：npm 自动下载的依赖包，像个巨大的黑盒，不用管它。
*   **`dist/`**：当你运行打包命令后生成的文件夹，里面是最终可以上传到服务器的静态网页。
*   **`package-lock.json`**：记录依赖包的精确版本，确保在别人电脑上运行效果一致。
 

Astro 会在 `src/pages/` 目录中查找 `.astro` 或 `.md` 文件。每个页面会根据其文件名作为路由暴露。

`src/components/` 目录没有什么特殊之处，但我们通常在这里放置任何 Astro/React/Vue/Svelte/Preact 组件。

`src/content/` 目录包含相关的 Markdown 和 MDX 文档的"集合"。使用 `getCollection()` 从 `src/content/blog/` 检索文章，并使用可选模式对 frontmatter 进行类型检查。查看 [Astro 内容集合文档](https://docs.astro.build/en/guides/content-collections/) 了解更多信息。

任何静态资源，如图片，可以放在 `public/` 目录中。

## 🧞 命令

所有命令都在项目根目录的终端中运行：

| Command                     | Action                                      |
| :-------------------------- | :------------------------------------------ |
| `npm install`               | 安装依赖                                     |
| `npm run dev`               | 在 `localhost:4321` 启动本地开发服务器         |
| `npm run build`             | 将生产站点构建到 `./dist/`                     |
| `npm run preview`           | 在部署前本地预览构建结果                        |
| `npm run astro ...`         | 运行 CLI 命令，如 `astro add`、`astro check`  |
| `npm run astro -- --help`   | 获取 Astro CLI 使用帮助                      |

