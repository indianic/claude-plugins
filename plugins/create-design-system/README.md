# create-design-system — Claude Code Plugin

Extract the visual language of any public website and generate a production-ready `design-md/<slug>/` folder containing a 9-section `DESIGN.md`, a `README.md`, and light + dark interactive preview HTML files — matching the format of the [awesome-design-md](https://github.com/VoltAgent/awesome-design-md) repo.

One command turns **"analyze this site"** into a complete, committable design system reference.

## What you get

For any URL you point it at, the plugin creates:

```
design-md/<slug>/
├── DESIGN.md          # 9-section design system (colors, typography, components, etc.)
├── README.md          # Summary + links
├── preview.html       # Light-theme interactive token catalog
└── preview-dark.html  # Dark-theme interactive token catalog (with header toggle)
```

Every section is backed by real hex codes, real font names, real pixel values — scraped from the site and synthesized into a specific, opinionated design direction. No AI slop.

## Usage

### Interactive mode — prompts for everything

```
/create-design-system
```

The plugin will ask three questions:
1. Which URL to analyze
2. Confirm or override the auto-suggested folder name (derived from the domain)
3. Which themes to generate — both / light only / dark only

### Non-interactive mode — one-shot

```
/create-design-system https://stripe.com
```

Auto-slugs the folder name from the domain (`stripe`), generates both light and dark previews, and skips all prompts. Ideal for automation or when you know exactly what you want.

### Natural language also works

The skill has trigger phrases registered, so any of these will activate it:

- `create a design system for https://vercel.com`
- `extract design system from linear.app`
- `add Notion to awesome-design-md`
- `generate DESIGN.md for https://framer.com`

## Installation

### Option A — Drop-in install (simplest)

Unzip the plugin folder into your Claude Code plugins directory:

**Project-level** (only for the current repo):
```bash
mkdir -p .claude/plugins
cp -r create-design-system .claude/plugins/
```

**User-level** (available in every project):
```bash
mkdir -p ~/.claude/plugins
cp -r create-design-system ~/.claude/plugins/
```

Then inside Claude Code:
```
/reload-plugins
```

### Option B — Install from a git repo

If you publish this plugin to a git repo, teammates can install directly:

```bash
cd ~/.claude/plugins
git clone https://github.com/<your-org>/create-design-system.git
```

Then `/reload-plugins` inside Claude Code.

### Option C — Manual install (no plugin loader)

If your team doesn't use the plugin system, you can install the skill and command directly:

```bash
# Project-level
mkdir -p .claude/skills .claude/commands
cp -r create-design-system/skills/create-design-system .claude/skills/
cp create-design-system/commands/create-design-system.md .claude/commands/

# User-level (available in every project)
mkdir -p ~/.claude/skills ~/.claude/commands
cp -r create-design-system/skills/create-design-system ~/.claude/skills/
cp create-design-system/commands/create-design-system.md ~/.claude/commands/
```

Then `/reload-plugins`.

## Verifying the install

After reloading, check that the skill and command are registered:

```
/help create-design-system
```

You should see the command description. You can also run `/reload-plugins` and look for `create-design-system` in the skills list printed in the output.

## Requirements

- Claude Code CLI (any recent version)
- Built-in `WebFetch` tool enabled (it is by default)
- No MCP server required
- No API keys or external services

## How it works

1. **Gathers inputs** — interactively via `AskUserQuestion` or from the CLI argument
2. **Researches the site** — parallel `WebFetch` calls on homepage + about + services/pricing pages to collect real design tokens
3. **Synthesizes a direction** — picks one signature move, one rationed brand accent, a specific font family, a shadow philosophy
4. **Writes four files** — uses the 9-section Stitch-extended format for `DESIGN.md` and reproduces the reference preview structure
5. **Verifies** — parses both HTML files for unclosed tags and reports line counts
6. **Offers README update** — asks whether to add the new entry to the root repo's collection list

## File structure inside the plugin

```
create-design-system/
├── .claude-plugin/
│   └── plugin.json                       # Plugin manifest
├── skills/
│   └── create-design-system/
│       └── SKILL.md                      # The skill definition + workflow
├── commands/
│   └── create-design-system.md           # Slash command wrapper
└── README.md                             # This file
```

## Quality guarantees

The skill enforces a strict quality bar:

- **No generic AI slop** — every claim backed by a hex code, font name, or pixel value
- **300+ line DESIGN.md depth** — matching the `claude/` and `indianic/` reference files
- **Warm vs. cool discipline** — neutrals must be consistently one or the other
- **Rationed brand color** — primary colors used as punctuation, not wallpaper
- **Fresh research per site** — explicit anti-pattern against copying existing palettes
- **Mandatory theme toggle** — both preview files must link to each other in the nav
- **Exactly 4 files** — no more, no less
- **HTML verification step** — both previews parsed for unclosed tags before completion

## License

MIT — see the parent repo for details.

## Credits

Built on top of the [awesome-design-md](https://github.com/VoltAgent/awesome-design-md) collection and the [Google Stitch DESIGN.md format](https://stitch.withgoogle.com/docs/design-md/format/).
