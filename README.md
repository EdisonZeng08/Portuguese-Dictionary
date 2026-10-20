# 葡语单词本 · Vocabulário（GitHub Pages 站点）

一个**零依赖、零后端**的葡语背单词单页应用，可直接托管在 GitHub Pages 上使用。

**特点**

- **不预装词汇**：词库完全由你自己录入，第一次打开是空词库和一份三步引导。
- **词性与变位**：可记录 12 种词性；动词能分别记录 `eu`、`tu`、`nós`、`você, ele, ela`、`vocês, eles, elas` 五个人称的变位。
- **分组 / 批量录入**：按主题分组，批量添加时每行 `葡语 / 释义 / 释义2`。
- **背诵**：卡片翻转，自评「不认识 / 模糊 / 认识」，按 1 / 2 / 4 / 8 / 16 天递进安排复习；可按分组、方向、范围和顺序筛选。
- **测验**：选释义、选单词、拼写、**动词变位**、混合题型；选择题干扰项优先取同词性的词。
- **导入导出**：JSON（含复习进度）与 CSV，可随时备份或搬家。
- **主题**：浅色 / 深色 / 跟随系统；响应式，手机上可用。

## 目录结构

```
.
├── index.html            # 应用本体（单文件，内联 CSS / JS）
├── favicon.svg           # 站点图标
├── manifest.webmanifest  # 添加到桌面 / 主屏用的 Web App Manifest
├── robots.txt
├── .nojekyll             # 告诉 GitHub Pages 不要用 Jekyll 处理静态文件
├── LICENSE
├── README.md
└── .github/
    └── workflows/
        └── deploy-pages.yml   # 可选：用 Actions 自动发布 Pages
```

整个站点只有 `index.html`、`favicon.svg`、`manifest.webmanifest` 三个文件是运行必需的，其余是说明和发布配置。

## 部署到 GitHub Pages（二选一）

### 方式 A：不用 Actions，直接发布（最简单）

1. 在 GitHub 新建一个**空仓库**（不要勾选初始化 README）。
2. 把这个目录里的文件上传到仓库根目录（`index.html` 必须在仓库根目录，不能套一层文件夹）。
   - 网页端：`Add file → Upload files`，把 `index.html`、`favicon.svg`、`manifest.webmanifest`、`robots.txt`、`.nojekyll` 一起拖上去，Commit。
   - 命令行（在**本目录**执行）：
     ```bash
     git init
     git add .
     git commit -m "葡语单词本 · Vocabulário"
     git branch -M main
     git remote add origin https://github.com/<你的用户名>/<仓库名>.git
     git push -u origin main
     ```
3. 仓库 **Settings → Pages**：
   - **Source** 选 `Deploy from a branch`
   - **Branch** 选 `main`，目录选 `/ (root)`
   - 保存，等 1–2 分钟。
4. 访问：`https://<你的用户名>.github.io/<仓库名>/`
   - 如果仓库名就叫 `<你的用户名>.github.io`，则直接访问 `https://<你的用户名>.github.io/`。

> `index.html` 里所有资源都是相对路径，放在仓库子路径（`/仓库名/`）下也能正常工作，不需要改任何代码。

### 方式 B：用 Actions 自动发布

仓库里已带 `.github/workflows/deploy-pages.yml`：

1. 推到 `main` 后，进入 **Settings → Pages**，把 **Source** 改成 `GitHub Actions`。
2. 之后每次 push 到 `main` 都会自动重新发布，构建日志在 **Actions** 标签页。
3. 首次运行如果失败，检查 **Settings → Actions → General → Workflow permissions** 是否允许 Actions 读取仓库内容。

> 两种方式二选一即可。切换方式时记得只保留一条发布链，避免互相覆盖。

## 本地预览

直接双击 `index.html` 即可（`file://` 也能用）。
想更接近线上环境，可以在本目录起一个静态服务器：

```bash
# Python 3
python -m http.server 8000
# 或 Node
npx serve .
```

然后打开 `http://localhost:8000/`。

> 注意：`file://` 与 `http://`、以及 `http://localhost` 与 `https://<用户名>.github.io` 都属于不同来源，**localStorage 不互通**。本地已录入的数据想带到线上，请用应用内「数据 → 导出 JSON」，再到线上站点「导入」。

## 数据说明（重要）

- 词库保存在**访问者自己浏览器**的 localStorage（键名 `pt_vocab_v1`），不会上传到 GitHub，站点也看不到任何人的词库。
- 换设备 / 换浏览器 / 清缓存 / 隐私模式会导致数据不互通或丢失，请用「数据 → 导出 JSON」定期备份。
- 页面顶部出现黄色提示条，说明浏览器不允许写本地存储，这次录入刷新后会丢失，请立刻导出。
- 纯静态页面，没有服务端，不收集任何用户数据，也不发起第三方请求。

## 更新站点

改完 `index.html` 后重新 push 即可（Actions 方式），或重新上传覆盖（分支方式）。

本目录的 `index.html` 是由多文件源码 `portuguese-vocab/`（`index.html` + `styles.css` + `app.js`）内联生成的。需要改功能时，建议改源码再重新生成，避免直接改单文件后与源码不一致：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File ..\_tools\build-pt-vocab-pages.ps1
```

## 许可

MIT，见 [LICENSE](LICENSE)。你可以自由使用、修改、再发布。

## 技术说明

- 单文件实现，无构建步骤、无第三方依赖、无 CDN / 外部字体请求。
- 兼容现代浏览器（Chrome / Edge / Firefox / Safari），支持添加到主屏（manifest）。
- 未注册 Service Worker，因此不保证完全离线可用（页面本身可被浏览器缓存）。
- 仓库为公开仓库时，GitHub Pages 免费；私有仓库发布 Pages 需要 GitHub Pro 等付费计划。
