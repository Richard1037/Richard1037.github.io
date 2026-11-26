# 个人主页（Astro 静态站）

一个纯静态的个人主页，构建产物不到 20 KB。设计目标是**长期可维护**：不依赖任何框架生态、不需要服务器、不需要数据库，五年后打开依然能改、能构建、能部署。

- **技术栈**：Astro 7（仅用于把内容渲染成静态 HTML），手写 CSS，约 25 行内联原生 JS（只用于主题切换）
- **托管**：GitHub Pages（通过 GitHub Actions 自动构建部署），纯静态文件，可换任意托管商
- **内容与布局分离**：所有文字集中在 `src/data/profile.ts` 一个文件里

---

## 目录结构

```
personal-site/
├── astro.config.mjs          # 构建配置（上线后要改 site 为你的域名）
├── .github/workflows/deploy.yml # GitHub Pages 自动部署工作流
├── package.json
├── .gitattributes            # 统一换行符
├── public/
│   ├── avatar.jpg            # 头像（已缩到 256×256）
│   ├── favicon.svg           # 标签页图标
│   ├── og.png                # 分享卡片图（1200×630，只被社交平台抓取）
│   └── robots.txt
└── src/
    ├── data/
    │   ├── profile.ts        # ★ 你的所有内容都在这里
    │   └── icons.ts          # 内联 SVG 图标的路径数据
    ├── styles/
    │   └── global.css        # 全部样式（设计变量在文件顶部的 :root）
    ├── layouts/
    │   └── BaseLayout.astro  # HTML 骨架、SEO、社交分享元数据、主题脚本
    ├── components/
    │   ├── Header.astro      # 顶部导航 + 深浅色切换
    │   ├── Hero.astro        # 首屏：姓名、签名、头像、信息卡、联系方式
    │   ├── About.astro       # 关于我 + 技能 + 兴趣 + 工具清单
    │   ├── Now.astro         # 最近在做什么
    │   ├── Projects.astro    # 项目作品集
    │   ├── Experience.astro  # 经历时间线（按类型配图标）
    │   ├── Colophon.astro    # 本站是怎么做出来的
    │   ├── Contact.astro     # 联系方式
    │   ├── ContactLink.astro # 联系方式按钮（含悬停提示）
    │   ├── Icon.astro        # 内联 SVG 图标渲染
    │   └── Footer.astro
    └── pages/
        └── index.astro       # 唯一的页面，把上面各板块拼起来
```

**你以后 95% 的时间只会打开 `src/data/profile.ts`。**布局和样式定了就不用再动。

---

## 本地运行

需要 Node.js 22.12 或更高版本（Astro 7 的要求；你现在装的是 24，没问题）。

```bash
cd "D:\AI Project\personal-site"

npm install        # 只需第一次
npm run dev        # 启动本地预览，打开 http://localhost:4321
npm run build      # 生成静态文件到 dist/
npm run preview    # 本地预览构建产物（模拟线上效果）
```

`npm run dev` 支持热更新：改了 `profile.ts` 存盘，浏览器立刻变，不用刷新。

> 可选：Astro 默认会发送匿名使用统计。想关掉就执行一次 `npx astro telemetry disable`。

---

## 改内容

打开 `src/data/profile.ts`，里面每个字段都有中文注释。需要你替换的占位内容都标了 `TODO`：

| 字段 | 说明 |
|---|---|
| `profile.name` / `nameLocal` | 英文名 / 中文名（中文名不要就设为 `''`） |
| `profile.signature` / `signatureSource` | 个性签名及其出处；出处留空则不显示 |
| `profile.avatar` | 头像路径。图片放 `public/`，**先缩到 256×256 再放**；留空则不显示 |
| `profile.subheadline` | **全站最重要的一句话** |
| `profile.facts` | 首屏右侧的分类信息卡，增删卡片改这个数组 |
| `profile.status` | 状态提示，留空则不显示 |
| `about` | 2~3 段自我介绍 |
| `skills` | 技能分组。每项可只写名字，也可 `{ name: 'C++', note: 'primary' }` |
| `interests` | 兴趣标签 |
| `uses` | 工具清单 |
| `projects` | 项目列表，建议 3~5 个 |
| `experiences` | 经历时间线，按时间倒序；每条可带 `links` 放相关链接 |
| `contactLinks` | 底部联系方式按钮（首屏显示前 3 个） |

### 项目描述怎么写

这是全站唯一需要动脑的地方。**不要**写：

> 使用 React + Node.js 实现了前后端分离的 Web 应用，支持用户登录和数据管理。

**要**写「问题 → 你的决策 → 结果」：

> 实验室的结果散落在命名混乱的表格里，找上学期的数据比重做实验还慢。我写了一个数据导入管道加全文检索界面；难点不在检索，而在设计一套人们真的愿意填的最小必填字段。现在 12 个实验室成员每周都在用。

三句话，招聘方读完就知道你会不会做事。

### 增删板块

删掉 `src/pages/index.astro` 里对应的那一行，同时删掉 `src/components/Header.astro` 里 `navItems` 中的对应项。

---

## 上线到 GitHub Pages

本项目已配置 `.github/workflows/deploy.yml`，每次推送到 `main` 分支后，GitHub Actions 会自动使用 Node 22 构建 `dist/` 并发布到 GitHub Pages。

### 1. 使用根仓库 `Richard1037.github.io`

在 GitHub 上将原仓库 `personal-site` 重命名为 `Richard1037.github.io`（或新建同名空仓库），并更新本地远程地址后推送：

```bash
cd "D:\AI Project\personal-site"

git remote set-url origin git@github.com:Richard1037/Richard1037.github.io.git
git push -u origin main
```

### 2. 开启 GitHub Pages（GitHub Actions）

1. 打开仓库 **Settings → Pages**
2. 在 **Build and deployment → Source** 中选择 **GitHub Actions**
3. 推送 `main` 分支后，等待 **Actions** 中的 `Deploy to GitHub Pages` 工作流跑完，即可通过以下地址访问：

```
https://richard1037.github.io/
```

### 3. 绑定自定义域名（可选）

如果以后买了自定义域名：
1. 在仓库 **Settings → Pages → Custom domain** 填入你的域名，并按 GitHub 提示配置 DNS（A / CNAME 记录）
2. 把 `astro.config.mjs` 里的 `site` 改成你的真实域名并推送：

```js
site: 'https://your-real-domain.com',
```

这一步影响 SEO 的规范链接（canonical）和微信/LinkedIn 的分享卡片，值得改。

---

## 日常更新流程

```bash
# 1. 改 src/data/profile.ts
# 2. 本地看一眼
npm run dev

# 3. 发布
git add .
git commit -m "Update projects"
git push
```

推送后 GitHub Actions 自动构建并上线，约一分钟。**你的电脑关机、重装、换新电脑，都不影响网站运行**——这就是「永久且和电脑状态无关」的实现方式。

---

## 长期维护清单

设个每年一次的日历提醒，做这几件事：

- [ ] **检查域名到期日**（如已绑定自定义域名），提前续费。域名过期 30 天后会进入高价赎回期，甚至被人抢注
- [ ] **留意 ICANN 注册人邮箱验证邮件**，收到就立刻点。漏点会导致域名被暂停
- [ ] **确认托管账号还能登录**（GitHub），2FA 恢复码还在
- [ ] **本地留一份源码备份**：`git clone` 到网盘或移动硬盘。GitHub 是你的主副本，但不该是唯一副本

另外建议在 [web.archive.org](https://web.archive.org/) 给首页存一个快照。这是内容层面的兜底，和托管商无关。

---

## 设计说明：为什么这么写

这些选择都是为了「五年后还能用」：

- **几乎没有客户端 JavaScript**：只有主题切换用到的约 25 行内联原生代码——没有水合、没有运行时依赖、不参与打包。**关掉 JS 网站依然完整可用**（内容、链接、悬停提示、跟随系统的深浅色都正常），只是"记住手动选择"失效
- **深色模式双触发**：默认跟随系统 `prefers-color-scheme`；手动切换存入 localStorage 并优先于系统设置。首屏脚本在绘制前执行，所以不会闪一下错误配色。切换按钮在无 JS 时自动隐藏，不会变成死按钮
- **背景动画只用 `transform`**：合成器能在 GPU 上处理，全视口覆盖几乎零成本。对比之下动画 `background-position` 需要每帧重绘整个视口
- **尊重 `prefers-reduced-motion`**：系统关闭动画时所有动效自动停止（**如果你看不到背景在动，先检查 Windows 的「设置 → 辅助功能 → 视觉效果 → 动画效果」是否被关掉了**）
- **系统字体栈**：不加载任何外部字体。没有 Google Fonts 请求，既快又不会因为第三方服务变动而失效
- **手写 CSS + 调色板变量**：颜色在 `:root` 里以 `--l-*`（浅色）和 `--d-*`（深色）各定义一次，中间只做一层映射。想换配色只改调色板，不会出现"改了深色忘了浅色"
- **内容集中在单个数据文件**：改文字永远不碰布局，布局永远不碰文字
- **纯静态 HTML 产物**：任何托管商都能跑。哪天想换托管商，5 分钟的事

---

## 常见问题

**改了内容但网页没变？**
`npm run dev` 下应该会热更新。线上则构建需要一分钟左右，去仓库的 **Actions** 页看构建日志。浏览器强刷 `Ctrl+Shift+R`。

**构建失败提示 Node 版本？**
Astro 7 需要 Node 22.12+。`.github/workflows/deploy.yml` 已固定使用 `node-version: 22`。

**想换配色？**
`src/styles/global.css` 顶部的 `:root` 里有 `--accent`（主色）、`--bg`、`--text` 等。深色模式在下面的 `@media (prefers-color-scheme: dark)` 块里。

**想加博客或简历下载页？**
在 `src/pages/` 下新建 `.astro` 文件即可，例如 `src/pages/blog.astro` 会生成 `/blog`。内容多的话可以把 `profile.ts` 拆成 `src/data/projects.ts`、`src/data/posts.ts` 等。
