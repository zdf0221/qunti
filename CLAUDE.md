# QunTI — Project Context for Claude

## 项目概述

**产品名：** QunTI（原计划 catbti 猫版，发现原作者 DogTI 已预告 CatTI，遂改题材）
**核心概念：** 12 题群友人格测试，MBTI 玩法，结果是群聊里 16 种经典原型。
**目标感觉：** 搞笑、有梗、离谱但说中了，适合截图发到群里。
**参考原型：** DogTI（dogti.pages.dev，作者 @乔纳森李）。本项目沿用其 UI/交互骨架。

---

## 设计原则（不要改动）

- **视觉风格：** 极简黑白像素风，和 DogTI 同源
- **配色：** 黑字白底，CSS 变量 `--black:#111 --gray-1:#f5f5f5 --gray-2:#e8e8e8 --gray-3:#aaa`
- **禁止：** 深色主题、复杂配色、严肃的 MBTI 风格
- **字体：** 标题用 `Press Start 2P`（Google Fonts CDN），正文用系统字体
- **按钮：** 黑色胶囊形（`border-radius: 100px`），白字，带播放圆圈图标
- **视口：** `body { max-width: 390px }` 手机单列

---

## 文件结构

```
/home/zdf/qt/qunti/
├── index.html          ← 主文件，唯一生产文件（CSS/JS 全内联）
├── CLAUDE.md           ← 本文件
├── QUIZ-LOGIC.md       ← 题目与结果逻辑对照表
├── README.md           ← 部署说明
├── dogti-*.{html,md}   ← DogTI 参考文件，实施时对照用
└── memory/             ← Claude 记忆系统
```

主文件是**单文件 HTML**，所有 CSS/JS 内联，零构建，零外部依赖（除 Google Fonts @import）。

---

## 页面结构（index.html）

三页通过 `.page` / `.page.active` 切换：

| 页面 | ID | 内容 |
|------|-----|------|
| 封面页 | `#page-home` | 大标题 QUNTI + 三行滚动像素头像 + 文案 + 开始按钮 |
| 题目页 | `#page-quiz` | 进度条 + 像素小头像气泡 + 问题文本 + 3 个选项(A/B/C) |
| 结果页 | `#page-result` | 大头像 + 结果名 + 副标题 + 金句 + 4 个性格标签 + 描述 + 分享/再测 |

### 首页关键实现
- 动物滚动带：CSS `@keyframes scrollLeft/scrollRight`，第 1、3 行向左，第 2 行向右
- 像素头像：`pixelSVG(key, size)` 从 `rolePixels[key]`（16×16 bitmap）生成内联 SVG
- 首次加载自动调用 `renderHomeMarquee()` 填充三行

---

## 测试逻辑

- **12 道题**，每题 3 个选项（A/B/C），每个选项标记一个 MBTI 维度（E/I、S/N、T/F、J/P）
- `calcResult()` 统计 4 维度多数，组合成 16 种 MBTI 类型
- 详细题目与维度映射见 → `QUIZ-LOGIC.md`

### 16 种结果（格式：XX 的 YY 群友原型）

| MBTI | 结果名 | role key |
|------|--------|---------|
| ENTJ | 在群里指点江山的群主 | groupowner |
| ENTP | 把所有话题都带偏的杠精 | debater |
| ENFJ | 比你还担心群氛围的和事佬 | peacemaker |
| ENFP | 一分钟换一个话题的水群机器 | topichopper |
| ESTJ | 把群规背下来的管理员 | admin |
| ESTP | 气氛一冷立刻开麦的整活担当 | entertainer |
| ESFJ | 谁发朋友圈都会去点赞的暖心派 | reactor |
| ESFP | 每天搬一车地狱笑话的搬史专家 | banshi |
| INTJ | 发言不多但一针见血的技术大佬 | techguru |
| INTP | 发了长文之后就消失的学术派 | theorist |
| INFJ | 凌晨两点发哲学长文的深夜玩家 | nightowl |
| INFP | 只发歌词和截图的文艺青年 | poet |
| ISTJ | 每天 8 点整打卡的自律标兵 | checkin |
| ISTP | 默默看戏关键时刻出手的扫地僧 | observer |
| ISFJ | 记得每个人生日的细心派 | caretaker |
| ISFP | 只发表情包不打字的静默艺术家 | sticker |

---

## 结果页文案规范

- 标签文字：**「你的群友格是」**
- 结果格式：`XX 的 YY`（16 种原型身份后缀）
- 后缀断行列表见 `formatRoleName()`，修改结果名时务必同步更新

---

## 像素头像

16 个头像存在 `rolePixels` 对象里，每个是一个 16 行的字符串数组，每行 16 字符：
- `' '`（空格）= 透明
- 其他字符（如 `#`）= 黑色像素

`pixelSVG(key, size)` 把 bitmap 转成内联 SVG `<rect>` 集合，`fill="#111"`。

三个尺寸用法：
- 首页跑马灯：`size=72`
- 题目页小头像：`size=40`
- 结果页大头像：`size=120`

---

## 部署

- **平台：** Cloudflare Pages（主用）→ 建议域名 `qunti.pages.dev`
- **流程：** 用户确认 OK → `git init` → push GitHub → Cloudflare Pages 自动拉取并重新部署
- **备选：** Vercel 也可以，同一仓库

---

## 用户偏好（重要）

- **只改文案时，不动设计和结构**
- 修改某一项时，只改被指定的那一项，其余保持原样
- 不要主动"顺手"优化代码、重构结构、添加功能
- 中文字符串里有引号时用 `「」`，不要用 ASCII `"`（会打断 JS 字符串）
