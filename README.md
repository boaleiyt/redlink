# RedLink

**RedLink 是一个免费、开源的 Obsidian 插件，把小红书（Xiaohongshu / RedNote）的收藏、点赞、个人笔记、关键词搜索结果和订阅账号内容，连同图片、视频、评论一起同步到本地 Obsidian，保存为标准 Markdown 文件。**

[English README](README.en.md) · [下载最新版](https://github.com/boaleiyt/redlink/releases/latest) · [问题反馈](https://github.com/boaleiyt/redlink/issues)

[![Version](https://img.shields.io/badge/version-1.5.1-blue)](manifest.json)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Obsidian](https://img.shields.io/badge/Obsidian-%3E%3D1.4.0-7c3aed)](https://obsidian.md)

## 一览

| 项目 | 说明 |
|---|---|
| 是什么 | Obsidian 插件，把小红书内容转成本地 Markdown |
| 运行环境 | Obsidian 桌面端（Windows / macOS / Linux），需 Obsidian ≥ 1.4.0 |
| 当前版本 | 1.5.1 |
| 许可与费用 | MIT，完全免费；无额度、无付费墙、无功能限制 |
| 数据流向 | 直连小红书官方接口；笔记与媒体只写入本地库，不上传服务器；Cookie 存本地 `data.json` |
| 登录方式 | 插件内扫码登录，无需账号密码 |
| 安装 | 手动放入三个文件 / `git clone` /（Obsidian 官方市场审核中） |
| 仓库 | https://github.com/boaleiyt/redlink |

---

## 它解决什么问题

小红书收藏夹是个黑洞 —— 存了几千条，再也没打开过；网页版会改版、笔记会删、账号可能被封，收藏内容说没就没。

RedLink 把小红书的内容**完整搬到本地 Obsidian**：正文、图片、视频、标签、评论、发布时间，全部落成标准 Markdown + 附件，**永久可控、全文可搜、可双链**。

### 与常见做法的区别

| 能力 | RedLink | 浏览器剪藏类工具 | 手动复制粘贴 |
|---|---|---|---|
| 批量同步整个收藏夹 | ✅ | ❌ 逐篇剪藏 | ❌ |
| 点赞 / 个人笔记 / 关键词搜索 / 订阅账号同步 | ✅ | ❌ | ❌ |
| 图片视频下载到本地 | ✅ 原图 + mp4 | ⚠️ 视工具而定 | ❌ |
| 评论一并保存 | ✅ 主评论 + 子评论 | ❌ | ❌ |
| 定时自动同步 | ✅ | ❌ | ❌ |
| 产出格式 | 标准 Markdown + YAML frontmatter，`![[...]]` 嵌入 | Markdown | 纯文本 |
| 数据归属 | 全部落在你自己的库里 | 本地 | 本地 |
| 费用 | 免费开源（MIT） | 多数免费 | 免费 |

---

## 核心功能

### 同步来源（4 类）

| 类型 | 说明 |
|---|---|
| **收藏** | 同步你的小红书收藏夹 |
| **点赞** | 同步你点过赞的笔记 |
| **个人帖子** | 同步自己发布的作品 |
| **专辑** | 按收藏专辑分别同步（可选） |

### 关键词搜索同步

按关键词自动搜索并同步结果，支持筛选：

- 排序：综合 / 最新 / 最多点赞
- 笔记类型：不限 / 视频 / 图文
- 时间范围、距离范围、位置范围

### 订阅账号同步

订阅指定博主，**按间隔自动拉取其新作品**，带随机延迟（最小/最大分钟数可配）避免触发风控。

### 完整内容抓取

- ✅ **正文** —— 文字内容、话题标签、@ 提及
- ✅ **图片** —— 全部原图，自动识别真实格式（webp / jpg / png / gif）
- ✅ **视频** —— 下载 mp4 到本地，正文嵌入 `![[...]]` 播放
- ✅ **评论** —— 主评论 + 子评论，含作者、头像、点赞数（条数上限可配）
- ✅ **元数据** —— 作者、发布时间、点赞数、评论数、原文链接

### AI 能力（可选，需自备 API）

- **AI 自动分类** —— 按内容归入自定义分类目录
- **AI 自动打标签** —— 生成 `aiTags` 写入 frontmatter

兼容任意 OpenAI 格式接口：填 `Base URL` + `API Key` + 模型名即可（默认 `https://api.openai.com/v1`）。

### 热点分析

配置热点分类，一键批量分析并生成报告笔记到指定目录。

### 自动化

| 项 | 可配 |
|---|---|
| 自动同步 | 开 / 关 + 间隔分钟数 |
| 自动搜索 | 开 / 关 + 间隔分钟数 |
| 自动订阅拉取 | 开 / 关 + 间隔 + 随机延迟区间 |
| 每批数量 | 每次请求拉取条数（1–20，建议 5） |
| **登录失效自动关闭** | 检测到登录过期时自动停掉定时同步，避免无效请求 |

---

## 笔记规范

### 目录结构

```text
<你设置的根目录>/
├── Bookmarks/                 # 收藏笔记
│   ├── 笔记标题.md
│   └── ...
├── Media/
│   └── 笔记标题/              # 每篇一个媒体目录
│       ├── 1.webp
│       ├── 2.webp
│       ├── 1.mp4
│       └── avatars/           # 评论头像缓存
└── ...
```

**笔记文件名 = 媒体目录名**（同源生成，保证 `![[...]]` 链接永不断）。

文件名清洗规则：`\ / : * ? " < > | # ^ [ ]` → `_`，压缩连续空白，截断 80 字，剥离首尾点和空格，空则 `untitled`。

### Frontmatter

```yaml
---
id: "62ecde26000000001202abc"                  # 小红书笔记 ID
title: "这版 UI 改完，我终于理解了什么叫呼吸感"    # 笔记标题
author: "某某设计师"                             # 作者昵称
type: 收藏                                       # 来源：收藏 / 点赞 / 个人帖子
syncTarget: bookmark                             # bookmark / like / post
url: "https://www.xiaohongshu.com/explore/..."   # 原文链接（含 xsec_token）
tags: ["UI设计", "版式", "设计灵感"]              # 小红书话题标签（内联数组）
status: 有效                                     # 有效性标记
source: xiaohongshu                              # 来源标识
rag:
  indexed: false                                 # 待索引标记
  keywords: []                                   # 关键词位（供 AI 管线填充）
createdAt: "2022-05-21T13:13:24.000Z"           # 发布时间（ISO 8601 带 Z）
syncedAt: "2026-09-29 14:30:43"                 # 同步时间
likes: 1234                                      # 点赞数
comments: 56                                     # 评论数
---
```

可选字段（按需出现）：

```yaml
aiTags: ["界面设计", "配色参考"]   # 仅启用「AI 打标签」时
category: "设计灵感"               # 仅启用「AI 分类」且归类成功时
```

> 字段值统一经 `JSON.stringify` 转义 → 字符串**带双引号**，数组为**内联形式** `["a", "b"]`，避免标题含特殊字符时破坏 YAML。

### 为 AI 检索预留的字段

生成的 frontmatter 内置知识库友好字段，可直接接入 RAG / AI 检索管线：

| 字段 | 默认值 | 含义 |
|---|---|---|
| `status` | `有效` | 有效性标记，便于筛选在用笔记 |
| `source` | `xiaohongshu` | 来源标识，多源知识库可区分出处 |
| `rag.indexed` | `false` | **待索引标记** —— 由你的索引管线改写为 `true` |
| `rag.keywords` | `[]` | **关键词位** —— 由你的关键词提取脚本填充 |

配合向量化 / 关键词提取脚本，即可搭出
**「小红书收藏 → 自动索引 → AI 可检索」** 的完整链路。

### 正文格式

```markdown
正文文字内容…

![[Media/笔记标题/1.webp]]
![[Media/笔记标题/2.webp]]
![[Media/笔记标题/1.mp4]]

## 评论

| 作者 | 内容 | 点赞 |
|---|---|---|
| ... | ... | ... |
```

- 图片 / 视频**一律用 `![[...]]` Obsidian 嵌入语法**（不用 `![]()`，避免标题含空格时断链）
- 评论渲染为 Markdown 表格

### 媒体下载策略

- 本地已有文件 → **跳过不重复下载**
- 下载失败或数据不可识别 → **不写链接**（宁可少图，绝不留断链）
- 图片真实格式用**魔数嗅探**校验，与扩展名不符时自动纠正
- 视频小于 1KB 视为无效，丢弃

---

## 安装

RedLink 是**纯静态插件**（三个文件、零依赖），任何能托管文件的地方都能分发它。下面从**最简单的安装方式**排起。

> 仓库地址：**https://github.com/boaleiyt/redlink**
> 下载页面：**https://github.com/boaleiyt/redlink/releases/latest**

### 方式一：官方插件市场（推荐，上架后可一键安装）

正在申请 Obsidian 官方插件市场上架。**上架后**：Obsidian → 设置 → 第三方插件 → 浏览 → 搜索 `RedLink` → 安装。

**这是最终形态**：不需要 GitHub、不需要代理、不需要手动放文件。上架前请用下面的方式。

### 方式二：手动安装（通用，不需要任何第三方插件）

只需要三个文件。**三种下载途径任选其一**，哪个通就用哪个。

**① 国内推荐 —— jsDelivr CDN**（点击即下载）

- [manifest.json](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/manifest.json)
- [main.js](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/main.js)
- [styles.css](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/styles.css)

**② 国内备选 —— GitHub 加速前缀**（三选一，把原链接接在前缀后）

```
https://ghproxy.net/https://github.com/boaleiyt/redlink/releases/download/1.5.1/main.js
https://ghfast.top/https://github.com/boaleiyt/redlink/releases/download/1.5.1/main.js
https://gh-proxy.com/https://github.com/boaleiyt/redlink/releases/download/1.5.1/main.js
```

（`manifest.json`、`styles.css` 同理，把最后的文件名换掉即可）

**③ 官方 Release 页** —— 能直连 GitHub 的用户直接访问
[github.com/boaleiyt/redlink/releases/latest](https://github.com/boaleiyt/redlink/releases/latest)

**安装步骤：**

1. 下载上面三个文件
2. 放进 `<你的库>/.obsidian/plugins/redlink/`（**目录名必须是 `redlink`**，没有就新建）
3. 重启 Obsidian → 设置 → 第三方插件 → 启用 **RedLink**

> 💡 找不到 `.obsidian` 文件夹？macOS / Linux 下以 `.` 开头的是隐藏目录，按 `Ctrl/Cmd + Shift + .` 显示。

### 方式三：git clone（开发者 / 能访问 GitHub 的用户）

```bash
cd <你的库>/.obsidian/plugins
git clone https://github.com/boaleiyt/redlink.git redlink
```

更新时在该目录 `git pull` 即可。**注意：仓库里的 `manifest.json` 与 Release 版本保持一致，克隆后直接可用。**

> 不推荐用 BRAT：它同样需要访问 `raw.githubusercontent.com`，且对 RedLink 这类三文件插件并无额外收益。能连 GitHub 的用户用上面方式三更直接，连不上的用户 BRAT 也用不了。

### 接口说明

本插件通过小红书 Web 接口获取数据，需**扫码登录获取你自己的 Cookie**（插件内提供登录按钮）。Cookie 仅存储在本地 `data.json`，不会上传到任何服务器。

---

## 使用

1. 打开 设置 → RedLink
2. 点「登录」/「重新登录」，**用小红书 App 扫码**
3. 设置根目录（笔记存哪）
4. 点「立即同步」

> ⚠️ 手机端登录小红书会踢掉 PC 会话，导致同步报「登录已过期」→ 重新扫码即可。

---

## 常见问题

### 它是什么、能解决什么

**Q：RedLink 是什么？**
A：RedLink 是一个免费、开源的 Obsidian 插件（MIT 许可），把小红书账号里的收藏、点赞、个人笔记、关键词搜索结果和订阅账号内容，连同图片、视频、评论一起同步成本地 Markdown 文件。仓库：https://github.com/boaleiyt/redlink

**Q：怎么把小红书收藏批量导出到本地？**
A：① 在 Obsidian 里装好 RedLink（三个文件手动安装，或 `git clone`）；② 插件内扫码登录小红书；③ 设置存储根目录，点「立即同步」。笔记按 `Bookmarks/`、`Media/` 分目录落盘，图片视频自动下载。

**Q：小红书收藏夹怎么备份，防止笔记被作者删掉？**
A：RedLink 把收藏内容（正文、图片、视频、评论、元数据）完整下载到本地磁盘；同步完成后即使原笔记被删、账号异常，本地副本仍能打开。建议打开「自动同步」，按设定间隔增量拉取。

**Q：能把小红书笔记保存成 Markdown 放进 Obsidian 吗？**
A：这是 RedLink 的默认产出。每篇笔记是一个 `.md` 文件，含 YAML frontmatter（`id` / `title` / `author` / `url` / `tags` / `likes` / `createdAt` 等）、正文、`![[...]]` 形式的内嵌媒体和评论表格，可直接被 Obsidian 全文检索、双链引用。

**Q：小红书的图片和视频能批量下载吗？**
A：能。图片按原图下载，用魔数校验真实格式（webp / jpg / png / gif）；视频下载 mp4 到本地并在正文内嵌播放。本地已存在的文件跳过，不重复下载。

**Q：RedLink 收费吗？有功能限制吗？**
A：完全免费。MIT 许可，无额度限制、无付费墙、无功能阉割。打赏自愿，不影响任何功能。

**Q：支持手机或 iPad 吗？**
A：不支持。RedLink 是桌面端插件（`isDesktopOnly: true`），运行于 Obsidian 桌面版的 Windows / macOS / Linux。

**Q：需要输入小红书账号密码吗？会被封号吗？**
A：不需要密码。登录走小红书官方扫码，Cookie 只存本地 `data.json`；请求直连官方接口，无第三方中转，且只读取你自己账号可见的内容。订阅拉取带随机延迟以降低请求频率。但任何自动化访问都存在平台风控的不确定性，无法承诺「绝不触发风控」。

**Q：RedLink 和 xhs2obsidian 是什么关系？**
A：RedLink 基于 [ytf606/xhs2obsidian](https://github.com/ytf606/xhs2obsidian) 二次开发。签名算法与 `x-rap-param` 拦截方案沿用原作者实现；RedLink 重做了界面与品牌、治理了定时器生命周期与请求超时、增加了图片数据校验和笔记命名/frontmatter 规范，并移除了原版的额度与付费逻辑。

**Q：同步下来的笔记能直接给 AI 或 RAG 用吗？**
A：可以。frontmatter 内置 `rag.indexed`（待索引标记）与 `rag.keywords`（关键词位），配合向量化或关键词提取脚本，即可搭出「收藏 → 索引 → 可检索」的链路。

### 故障排查

**Q：同步提示「登录已过期」？**
A：Cookie 失效。点「重新登录」重新扫码。手机端登录会踢掉电脑端会话。

**Q：点同步没反应，只提示「已全部同步完毕」？**
A：插件认为已同步到底了。点设置里的「清除缓存」后重新同步（会重走一遍全库，但已同步的会跳过写盘）。

**Q：视频没有下载？**
A：视频体积大、下载慢，失败时不会写入链接。可重新同步该篇。

**Q：AI 功能怎么用？**
A：需要你自己的 OpenAI 兼容接口。填入 Base URL、API Key、模型名，然后开启「AI 分类」或「AI 打标签」。

---

## 隐私说明

- **所有数据只存本地**，不上传任何服务器
- Cookie 与 API Key 存在库目录下的 `data.json`
- ⚠️ **请不要把 `data.json` 提交到公开仓库**（仓库已内置 `.gitignore` 排除）
- 请求直连小红书官方接口，无中转

---

## 致谢

本项目基于 **[ytf606/xhs2obsidian](https://github.com/ytf606/xhs2obsidian)**（原名 XHS Sync）二次开发。

签名算法与 `x-rap-param` 拦截方案的原始实现来自原作者，RedLink 在其基础上完成了：

- 界面重做与品牌统一
- 定时器生命周期治理、请求超时保护
- 图片数据校验（杜绝断链）
- 笔记命名与 frontmatter 规范化
- 免费开源化（移除原版的额度/付费逻辑）

感谢原作者的探索。

---

## 许可

MIT License

---

## 支持这个项目

RedLink 完全免费、开源，**没有任何付费墙、功能限制或额度限制**。所有功能都可以无偿使用。

如果它确实帮到了你，愿意请我喝杯咖啡的话 —— 完全自愿，不影响任何功能：

<p align="center">
  <img src="docs/donate-wechat.jpg" alt="微信赞赏" width="200" />
  &nbsp;&nbsp;&nbsp;
  <img src="docs/donate-alipay.jpg" alt="支付宝" width="200" />
</p>

<p align="center"><sub>左：微信 &nbsp;|&nbsp; 右：支付宝</sub></p>

也欢迎用这些**免费**的方式支持：

- ⭐ 给仓库点个 Star
- 🐛 遇到问题提 [Issue](https://github.com/boaleiyt/redlink/issues)
- 📣 推荐给有同样需求的朋友
