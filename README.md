# 移动端音乐播放器（毕业设计 · 前端）

2019 年本科毕业设计的实作项目——一个移动端风格的音乐播放器。
**原生 JavaScript 手写，没有用任何框架**，目的是把"用 JS 从头实现一个完整 Web 应用"这件事走一遍。

配套论文：《用 Javascript 实现 Web 音乐播放器》。

---

## 功能

- 播放 / 暂停、上一首 / 下一首、循环切换
- 进度条拖动（touch 事件）+ `requestAnimationFrame` 实时进度动画
- 播放列表弹层、喜欢收藏标记
- 封面背景用 **Canvas 手写高斯模糊**（不是 CSS filter）

## 代码结构

逻辑按职责拆成 7 个模块，入口 `index.js` 负责装配：

| 文件 | 职责 |
|---|---|
| `src/js/index.js` | 入口，把各模块拼起来 |
| `src/js/render.js` | DOM 渲染（保留着大段注释掉的旧实现，是重构过程的痕迹） |
| `src/js/audioManage.js` | `Audio` 元素封装：播放 / 暂停 / 跳转 |
| `src/js/controlManage.js` | 控制逻辑：上一首 / 下一首、环形索引 |
| `src/js/processor.js` | 进度条拖动与动画 |
| `src/js/playlist.js` | 播放列表弹层 |
| `src/js/gaussBlur.js` | Canvas 高斯模糊，给封面做背景 |
| `src/less/index.less` | 样式源码（编译到 `dist/css/`） |

`dist/` 是构建产物，**已提交进仓库，可以直接用，不必先构建**。

## 怎么跑

**用静态服务器把 `dist/` 当根目录**（推荐）：

```bash
npx serve dist                 # 打开提示的地址即可
# 或者
python -m http.server 8090 --directory dist   # http://localhost:8090
```

> ⚠️ **不要直接双击 `dist/index.html`**，有两个原因：
> 1. 页面用 XHR 读取 `dist/mock/data.json`，浏览器在 `file://` 协议下会拦截这个请求；
> 2. `data.json` 里的资源写的是根路径 `/source/xxx.mp3`，只有在 dist 被当作站点根目录时才解析得到。

### 想改 less 后重新构建

原构建链是 Gulp 3.9 + `gulp-connect`（见 `gulpfile.js`：root=dist、端口 8090）。

Gulp 3 依赖的旧 `graceful-fs` 在现代 Node（16+）上装不起来。所以要么在 Node 12 下 `npm install`，
要么直接改 `dist/css/` 下的编译产物：

```bash
npm install --ignore-scripts      # 老依赖，可能需要 Node 12
npx gulp                          # 编译 less → dist/css 并启动 8090
```

## 相关

- `index.docx` —— 当年的设计文档
- **姊妹仓库 `node-musicPlayer`（已归档）** —— 同一个前端的**部署版**：把 `src/` 原样搬进 `static/`，
  外面套了一层 Express + LeanCloud 云引擎用来上线，外加一个"仅用于毕业设计"的首屏页。
  前端内容与本仓库完全重复，仅有约 20 行 `server.js` 的部署逻辑是新增的。

  当年的大致做法：

  ```js
  AV.init({ appId: process.env.LEANCLOUD_APP_ID, appKey: process.env.LEANCLOUD_APP_KEY });
  app.listen(process.env.LEANCLOUD_APP_PORT || 3000);
  ```

  🔑 **密钥一律走环境变量——不要把 appId / appKey / masterKey 写进代码。**

## ⚠️ 注意事项

- `dist/source/` 里打包了若干首**完整歌曲与角色图片**，仅作个人学习用途，
  **请勿公开分发或上线**（避免版权问题）。
- `node_modules/` 曾被误提交进版本控制（约 9 万个文件），已在本仓库中移出跟踪。
  需要时自行 `npm install`。
- 仓库里没有 `.gitignore` 的历史遗留：现在补上了，见根目录 `.gitignore`。
