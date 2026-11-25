# RedLink

**RedLink is a free, open-source Obsidian plugin that exports Xiaohongshu (小红书 / RedNote) bookmarks, likes, your own posts, keyword search results, and subscribed creators into a local Obsidian vault as standard Markdown — with images, videos, and comments downloaded alongside.**

[中文说明](README.md) · [Download](https://github.com/boaleiyt/redlink/releases/latest) · [Issues](https://github.com/boaleiyt/redlink/issues)

[![Version](https://img.shields.io/badge/version-1.5.1-blue)](manifest.json)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Obsidian](https://img.shields.io/badge/Obsidian-%3E%3D1.4.0-7c3aed)](https://obsidian.md)

## At a glance

| Item | Detail |
|---|---|
| What it is | An Obsidian plugin that converts Xiaohongshu content into local Markdown |
| Runs on | Obsidian desktop (Windows / macOS / Linux), Obsidian ≥ 1.4.0 |
| Current version | 1.5.1 |
| License & cost | MIT. Free, with no quotas, paywalls, or feature locks |
| Data flow | Talks directly to Xiaohongshu's official API; notes and media are written to your local vault only |
| Login | QR-code login inside the plugin — no username or password required |
| Install | Drop in three files / `git clone` / (Obsidian community directory under review) |

## The problem it solves

A Xiaohongshu bookmark folder is where saved posts go to die. You save thousands of notes and never open them again; the web version changes, posts get deleted by their authors, and accounts can be restricted. The content you thought you had saved simply disappears.

RedLink copies that content — body text, images, videos, tags, comments, and timestamps — into your own vault as Markdown plus attachments, so it stays searchable, linkable, and yours.

## Features

### Sync sources

| Source | What it does |
|---|---|
| **Bookmarks** | Syncs your saved notes |
| **Likes** | Syncs notes you have liked |
| **Own posts** | Syncs notes you published |
| **Collections (albums)** | Syncs each album separately, optional |

### Keyword search sync

Runs a Xiaohongshu search for a keyword and syncs the results, with filters for sort order (relevance / newest / most liked), note type (video / image), time range, distance, and location.

### Creator subscription sync

Subscribe to specific creators and pull their new posts on an interval, with a configurable random delay (min/max minutes) to keep the request rate low.

### Complete content capture

- **Body text** — text, hashtags, @ mentions
- **Images** — all originals, real format detected (webp / jpg / png / gif)
- **Videos** — downloaded as mp4 and embedded in the note
- **Comments** — top-level and nested, with author, avatar, and like count
- **Metadata** — author, publish time, likes, comment count, original URL

### Optional AI features (bring your own API)

- **Auto-categorisation** into custom folders
- **Auto-tagging** into an `aiTags` frontmatter field

Works with any OpenAI-compatible endpoint: set `Base URL`, `API Key`, and model name.

### Automation

Auto-sync, auto-search, and auto-subscription pull each have their own interval. Timed sync automatically stops when the login expires, so it does not keep firing useless requests.

## What the output looks like

```text
<vault>/<your root folder>/
├── Bookmarks/
│   └── Note title.md
└── Media/
    └── Note title/
        ├── 1.webp
        ├── 2.webp
        └── 1.mp4
```

Each note carries YAML frontmatter:

```yaml
---
id: "62ecde26000000001202abc"
title: "Note title"
author: "Author name"
type: 收藏
syncTarget: bookmark
url: "https://www.xiaohongshu.com/explore/..."
tags: ["UI design", "layout"]
source: xiaohongshu
rag:
  indexed: false
  keywords: []
createdAt: "2022-05-21T13:13:24.000Z"
likes: 1234
comments: 56
---
```

The `rag.indexed` and `rag.keywords` fields exist so the notes can drop straight into a retrieval pipeline: sync → index → query with your own AI stack.

Media is embedded with Obsidian's `![[...]]` syntax rather than `![]()`, so titles containing spaces never produce broken links. Images are validated by magic-number sniffing, and files that fail to download are never written as dead links.

## How it compares

| Capability | RedLink | Browser clippers | Manual copy-paste |
|---|---|---|---|
| Sync an entire bookmark folder | ✅ | ❌ one note at a time | ❌ |
| Likes / own posts / keyword search / subscriptions | ✅ | ❌ | ❌ |
| Download images and videos locally | ✅ originals + mp4 | ⚠️ depends on tool | ❌ |
| Save comments | ✅ top-level and nested | ❌ | ❌ |
| Scheduled sync | ✅ | ❌ | ❌ |
| Output format | Markdown + YAML frontmatter | Markdown | plain text |
| Cost | Free, open source (MIT) | Usually free | Free |

## Installation

RedLink is a static plugin: three files, no dependencies.

### Option 1 — manual install (works everywhere)

Download these three files:

- [manifest.json](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/manifest.json)
- [main.js](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/main.js)
- [styles.css](https://cdn.jsdelivr.net/gh/boaleiyt/redlink@1.5.1/styles.css)

Put them in `<your vault>/.obsidian/plugins/redlink/` (the folder name must be `redlink`), restart Obsidian, then enable **RedLink** under Settings → Community plugins.

If GitHub is slow from your network, prefix the release URLs with a mirror such as `https://ghfast.top/`.

### Option 2 — git clone

```bash
cd <your vault>/.obsidian/plugins
git clone https://github.com/boaleiyt/redlink.git redlink
```

### Option 3 — Obsidian community directory

Submission is under review. Once accepted, install it from Settings → Community plugins → Browse → search `RedLink`.

## Usage

1. Settings → RedLink
2. Click **Log in** and scan the QR code with the Xiaohongshu mobile app
3. Choose a root folder for the notes
4. Click **Sync now**

> Logging in on your phone kicks the desktop session out of Xiaohongshu, which makes RedLink report an expired login. Scan again to recover.

## FAQ

**What is RedLink?**
A free, open-source (MIT) Obsidian plugin that syncs Xiaohongshu bookmarks, likes, posts, keyword search results, and subscribed creators into a local vault as Markdown, including images, videos, and comments.

**How do I export my Xiaohongshu bookmarks?**
Install RedLink in Obsidian, scan the QR code to log in, pick a root folder, and press Sync. Notes land under `Bookmarks/`, media under `Media/`, one folder and one Markdown file per note.

**Can I back up my Xiaohongshu saves before the author deletes them?**
Yes — that is the main use case. Everything is downloaded to your disk, so the local copy survives deletion of the original post or changes to your account state. Turning on auto-sync keeps the backup incremental.

**Does it save posts as Markdown I can use in Obsidian?**
Yes. Each note is a `.md` file with YAML frontmatter, body text, embedded media, and a comments table, fully searchable and linkable inside Obsidian.

**Can it download Xiaohongshu images and videos in bulk?**
Yes. Images are fetched at original resolution and validated by magic numbers; videos are saved as mp4 and embedded. Files already on disk are skipped.

**Is RedLink free?**
Yes. MIT licensed, no quotas, no paywall, no crippled tier. Donations are optional and unlock nothing.

**Does it work on mobile or iPad?**
No. It is a desktop-only plugin (`isDesktopOnly: true`) for Obsidian on Windows, macOS, and Linux.

**Do I have to give it my Xiaohongshu password?**
No. Login uses Xiaohongshu's official QR flow; the resulting cookie is stored locally in `data.json`. Requests go straight to Xiaohongshu with no third-party relay, and only content visible to your own account is read. Subscription pulls use randomised delays to keep request rates low, but no automation can guarantee it will never trip platform risk controls.

**How is this different from xhs2obsidian?**
RedLink is a fork of [ytf606/xhs2obsidian](https://github.com/ytf606/xhs2obsidian). The signing algorithm and `x-rap-param` interception come from the original author. RedLink rebuilt the UI and branding, fixed timer lifecycle and request timeouts, added image integrity checks and standardised note naming/frontmatter, and removed the original quota and payment logic.

**Can I feed the synced notes to an LLM or a RAG pipeline?**
Yes. The frontmatter ships `rag.indexed` and `rag.keywords` fields for exactly that.

**Sync says "login expired".**
The cookie is stale. Log in again and rescan. Logging in on mobile invalidates the desktop session.

**Sync finishes instantly saying everything is already synced.**
The plugin thinks it has reached the end. Use **Clear cache** in settings and sync again; existing notes are skipped rather than rewritten.

## Privacy

- All data stays on your machine; nothing is uploaded to any server
- Cookie and API key live in `data.json` inside your vault
- Do not commit `data.json` to a public repository (a `.gitignore` rule is included)
- Requests go directly to Xiaohongshu's official API, with no intermediary

## Credits

Built on [ytf606/xhs2obsidian](https://github.com/ytf606/xhs2obsidian). Thanks to the original author for the signing and request-interception work.

## License

MIT
