# RedLink

> 免费开源的小红书 → Obsidian 同步插件。把收藏、点赞、个人帖子、关键词搜索结果和订阅账号的笔记，连图带视频一起搬进你的知识库。

[![Version](https://img.shields.io/badge/version-1.5.0-blue)](manifest.json)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Obsidian](https://img.shields.io/badge/Obsidian-%3E%3D1.4.0-7c3aed)](https://obsidian.md)

---

## 它解决什么问题

小红书收藏夹是个黑洞 —— 存了几千条，再也没打开过；网页版会改版、笔记会删、账号可能被封，收藏内容说没就没。

RedLink 把小红书的内容**完整搬到本地 Obsidian**：正文、图片、视频、标签、评论、发布时间，全部落成标准 Markdown + 附件，**永久可控、全文可搜、可双链**。

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

> 仓库地址：**https://github.com/boaleiyt/redlink**
> 下载页面：**https://github.com/boaleiyt/redlink/releases/latest**

### 方式一：BRAT（推荐，支持自动更新）

1. 在 Obsidian 中安装 **BRAT** 插件（[安装说明](https://github.com/TfTHacker/obsidian42-brat)）
2. 打开 BRAT 设置 → **Add Beta plugin**
3. 填入：`boaleiyt/redlink`
4. 点 **Add Plugin** → 回到「第三方插件」启用 **RedLink**

> BRAT 会自动跟踪本仓库的 Release，以后有新版本一键更新。

### 方式二：手动安装

1. 到 [Releases](https://github.com/boaleiyt/redlink/releases/latest) 下载三个文件：
   - `main.js`
   - `manifest.json`
   - `styles.css`
2. 放进 `<你的库>/.obsidian/plugins/redlink/`（**目录名必须是 `redlink`**）
3. Obsidian → 设置 → 第三方插件 → 关闭安全模式 → 启用 **RedLink**

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
