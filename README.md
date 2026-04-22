# QunTI · 群友人格测试

12 题群友 MBTI，测一下你是群聊里的哪种原型。灵感来自 [DogTI](https://dogti.pages.dev/)（同款玩法，场景换成群聊）。

## 本地跑起来

不需要构建，直接起静态服务：

```bash
cd /home/zdf/qt/catbti
python3 -m http.server 8000
# 打开 http://localhost:8000/
```

或者双击 `index.html`（file:// 协议下 Google Fonts 的远程字体可能加载失败，推荐走 http.server）。

## 文件清单

| 文件 | 作用 |
|------|------|
| `index.html` | 主文件。CSS/JS 全内联，所有文案、题库、16 种结果、16 个像素头像都在里面 |
| `CLAUDE.md` | 项目上下文（给未来的 AI/维护者看） |
| `QUIZ-LOGIC.md` | 12 道题与 16 种结果的维度映射表 |
| `dogti-*.{html,md}` | DogTI 原版参考文件，本地开发对照用，部署时可不上传 |
| `memory/` | Claude 记忆系统，本地用 |

## 部署

### Cloudflare Pages（推荐）

1. `git init && git add index.html CLAUDE.md QUIZ-LOGIC.md README.md && git commit -m "init"`
2. `gh repo create qunti --public --source=. --push`（或手动在 GitHub 新建仓库后 push）
3. Cloudflare Dashboard → Pages → Create project → Connect to Git → 选 `qunti` 仓库
4. Build 配置：**无需构建**，Build command 留空，Output directory 填 `/`
5. 部署后会拿到 `qunti.pages.dev` 这样的域名

### Vercel

```bash
npx vercel --prod
```
根目录放 `index.html` 就行，没有 build step。

## 修改指南

| 想改什么 | 改哪里 |
|---------|-------|
| 题目文字 / 选项 / 维度打分 | `index.html` 里 `const questions = [...]` 数组 |
| 结果名 / 副标题 / 金句 / 标签 / 描述 | `const results = {...}` 对象 + `typeMap` + `formatRoleName()` 的 suffix 列表（三处保持同步） |
| 16 个像素头像 | `const rolePixels = {...}`，每个是 16 行 ×16 字符的 bitmap |
| 配色 | 文件顶部 `:root` CSS 变量 |
| 字体 | `<link>` 换 Google Fonts URL，或改 `--pixel` 变量 |

> ⚠️ 中文字符串里的引号用「」（全角），不要用 ASCII `"`，否则会打断 JS 字符串。

## 免责

灵感来自 DogTI（作者 @乔纳森李），玩法与数据结构沿用原版，题目与结果文案为本项目原创。永久免费，禁止商用。
