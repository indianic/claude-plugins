<div align="center">

# IndiaNIC Claude Code Plugins

**Production-ready plugins for [Claude Code](https://claude.com/claude-code) — built by the team behind [cohort.indianic.dev](https://cohort.indianic.dev).**

[![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin_Marketplace-D97757?style=for-the-badge&logo=anthropic&logoColor=white)](https://claude.com/claude-code)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
[![Homepage](https://img.shields.io/badge/homepage-cohort.indianic.dev-0ea5e9?style=for-the-badge)](https://cohort.indianic.dev/plugin)

One command. Real design tokens. No AI slop.

[Install](#-install) · [Plugins](#-plugins) · [About cohort.indianic.dev](#-about-cohortindianicdev) · [Contribute](#-contributing)

</div>

---

## ✨ What's inside

This is a **Claude Code plugin marketplace** published by [IndiaNIC](https://www.indianic.com/). It ships opinionated, production-grade plugins that slot straight into your Claude Code CLI with a single command.

Currently shipping:

| Plugin | What it does | Command |
| --- | --- | --- |
| **[create-design-system](plugins/create-design-system)** | Extracts a full 9-section design system from any public website and generates a 4-file `DESIGN.md` folder with light + dark interactive previews. | `/create-design-system <url>` |

More plugins are on the way — follow [cohort.indianic.dev/plugin](https://cohort.indianic.dev/plugin) for updates.

---

## 🚀 Install

### One-command install (recommended)

Inside an active Claude Code session, run these two slash commands:

```bash
/plugin marketplace add indianic/claude-plugins
/plugin install create-design-system@cohort-work
```

That's it. The plugin is now registered globally in `~/.claude/plugins/` and the `/create-design-system` slash command is live. Start a fresh Claude Code session (or run `/reload-plugins`) if it doesn't appear immediately.

### Project-level install

Want the plugin scoped to a single repo instead of your whole account? Add `--scope project` to the install step:

```bash
/plugin marketplace add indianic/claude-plugins
/plugin install create-design-system@cohort-work --scope project
```

### From a private fork or mirror

If you're cloning this repo internally, point the marketplace at the local directory:

```bash
git clone https://github.com/indianic/claude-plugins.git ~/src/claude-plugins
/plugin marketplace add ~/src/claude-plugins
/plugin install create-design-system@cohort-work
```

### Remove or update

```bash
/plugin uninstall create-design-system          # remove the plugin
/plugin marketplace update cohort-work          # pull the latest version
/plugin marketplace remove cohort-work          # drop the marketplace entirely
```

---

## 🧩 Plugins

### `create-design-system`

> Paste any public URL. Get back a production-ready `DESIGN.md` folder — 9-section spec, real hex tokens, typography, and two interactive previews.

<details>
<summary><b>What it generates</b></summary>

For every URL you analyze, the plugin produces:

```
design-md/<slug>/
├── DESIGN.md          # 9-section design system (colors, typography, components, layout, depth, voice, responsive, agent prompt)
├── README.md          # Summary + links
├── preview.html       # Light-theme interactive token catalog
└── preview-dark.html  # Dark-theme interactive token catalog (with header toggle)
```

Every value — hex codes, font names, pixel measurements — is grounded in real research from the site. No generic palettes, no copy-paste recipes.

</details>

<details>
<summary><b>How to use it</b></summary>

**One-shot mode** (fastest):
```
/create-design-system https://stripe.com
```

**Interactive mode** (guided):
```
/create-design-system
```
It asks three questions — URL, folder name, theme mode — and generates both light and dark previews by default.

**Natural language** also works inside any Claude Code conversation:
- `create a design system for https://vercel.com`
- `extract design system from linear.app`
- `add Notion to awesome-design-md`

</details>

<details>
<summary><b>Quality guarantees</b></summary>

The skill enforces a strict bar so you never ship AI slop:

- **Backed by real data** — every claim tied to a hex, font, or pixel value pulled from the live site
- **300+ line `DESIGN.md` depth** — matching the reference files in [`awesome-design-md`](https://github.com/VoltAgent/awesome-design-md)
- **Warm vs. cool discipline** — neutrals are consistently one or the other
- **Rationed brand color** — primary colors used as punctuation, not wallpaper
- **Fresh research per site** — explicit anti-pattern against copying existing palettes
- **Mandatory theme toggle** — both preview files link to each other in the nav
- **HTML verification** — both previews parsed for unclosed tags before completion

</details>

<details>
<summary><b>Requirements</b></summary>

- Claude Code CLI (any recent version)
- Built-in `WebFetch` tool (enabled by default)
- No MCP servers, API keys, or external services

</details>

[Full plugin README →](plugins/create-design-system/README.md)

---

## 🌐 About cohort.indianic.dev

[**cohort.indianic.dev**](https://cohort.indianic.dev) is IndiaNIC's internal design-engineering cohort — a curated collection of design systems, Claude Code plugins, and AI-assisted tooling that the team uses every day.

The site hosts:

- 🎨 **Design gallery** — dozens of extracted design systems (Stripe, Vercel, Linear, Framer, and more), each with a 9-section `DESIGN.md` and live dark/light previews
- 🔌 **Plugin marketplace** — this repo, served with copy-paste install snippets at [cohort.indianic.dev/plugin](https://cohort.indianic.dev/plugin)
- 📚 **Reference patterns** — opinionated conventions for modern frontend work (Next.js, Tailwind, design tokens, accessibility)

Everything is built in the open so the rest of the team — and now the community — can benefit from the same tooling.

> 💡 **Tip:** the [cohort.indianic.dev/plugin](https://cohort.indianic.dev/plugin) page auto-detects your host and renders copy-ready install commands, so non-Claude-Code users can still grab the plugin as a plain tarball download.

---

## 📦 Marketplace manifest

This repository is a native **Claude Code marketplace**. The manifest lives at [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) and is validated against Anthropic's [marketplace schema](https://anthropic.com/claude-code/marketplace.schema.json).

```
.
├── .claude-plugin/
│   └── marketplace.json        ← marketplace manifest Claude Code reads
├── plugins/
│   └── create-design-system/   ← the actual plugin folder
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   └── create-design-system.md
│       ├── skills/
│       │   └── create-design-system/
│       │       └── SKILL.md
│       ├── README.md
│       └── INSTALL.md
├── LICENSE
└── README.md                   ← you are here
```

Anyone can fork this repo, add their own plugin folder under `plugins/`, extend the `plugins` array in `marketplace.json`, and republish — it just works.

---

## 🛠 Contributing

Found a bug? Want to add a plugin? PRs welcome.

1. Fork this repo
2. Add your plugin under `plugins/<your-plugin-name>/` following the [Claude Code plugin spec](https://docs.claude.com/en/docs/claude-code/plugins)
3. Register it in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) under the `plugins` array
4. Open a PR with a clear description of what your plugin does and a screenshot / usage example

For internal IndiaNIC contributions, ping `@skmundra` or the `#cohort-dev` channel.

---

## 📝 Changelog

### `1.0.1` — 2026-04-09
- Marketplace manifest source path hardened to `./plugins/create-design-system` so cached clones from the earliest exploration commits refresh cleanly. If you hit `Source path does not exist` on `1.0.0`, run `/plugin marketplace update cohort-work` (or remove + re-add the marketplace) and try again.
- No plugin-content changes — `create-design-system` behaviour is identical to `1.0.0`.

### `1.0.0` — 2026-04-08
- Initial public release of the `cohort-work` marketplace.
- Ships `create-design-system` — 9-section `DESIGN.md` generator with light + dark interactive previews.

---

## 📄 License

MIT © [IndiaNIC](https://www.indianic.com/) — see [LICENSE](LICENSE) for details. Plugin content may carry additional credits; see each plugin's individual README.

## 🙏 Credits

- [`awesome-design-md`](https://github.com/VoltAgent/awesome-design-md) — the collection format that `create-design-system` targets
- [Google Stitch `DESIGN.md` spec](https://stitch.withgoogle.com/docs/design-md/format/) — the 9-section structure
- [Anthropic](https://claude.com) — for Claude Code and the plugin architecture

---

<div align="center">

Built with ❤️ by the team at **[IndiaNIC](https://www.indianic.com/)** · Powered by **[Claude Code](https://claude.com/claude-code)**

**[cohort.indianic.dev/plugin](https://cohort.indianic.dev/plugin)**

</div>
