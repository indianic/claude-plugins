# Quick Install Guide

## For teammates receiving this bundle

You've been given a `create-design-system` plugin for Claude Code. Here's the 60-second install:

### 1. Unzip (if you got a tarball)

```bash
tar -xzf create-design-system-v1.0.0.tar.gz
```

You should now have a folder called `create-design-system/`.

### 2. Drop it into your Claude Code plugins directory

**For all your projects** (recommended):
```bash
mkdir -p ~/.claude/plugins
mv create-design-system ~/.claude/plugins/
```

**For just one project**:
```bash
mkdir -p .claude/plugins
mv create-design-system .claude/plugins/
```

### 3. Reload Claude Code

Inside an active Claude Code session:
```
/reload-plugins
```

Or just start a new session.

### 4. Use it

```
/create-design-system https://stripe.com
```

Or interactively:
```
/create-design-system
```

Claude will ask you three questions (URL, folder name, theme mode) and then generate a full 4-file design system folder.

## Troubleshooting

**`/create-design-system` not found after install**
- Run `/reload-plugins` — check the output includes `create-design-system` in the skills count
- Verify the folder structure: `ls ~/.claude/plugins/create-design-system/skills/create-design-system/SKILL.md` should exist
- Make sure the SKILL.md frontmatter (the block between `---` lines at the top) is intact — a broken YAML header silently drops the skill

**`WebFetch` errors when running the skill**
- `WebFetch` is a built-in Claude Code tool. If disabled, enable it in your Claude Code settings.
- Sites behind authentication or Cloudflare challenges may fail to fetch — try a different public page on the same domain.

**Output folder has only a stub DESIGN.md**
- The site may be a JavaScript-rendered SPA that `WebFetch` can't parse. The skill will still produce a file, but quality will be lower. Consider combining with a browser-capable MCP (e.g. `mcp__plugin_superpowers-chrome_chrome__use_browser`) for richer scraping.

## Uninstall

```bash
rm -rf ~/.claude/plugins/create-design-system
```

Then `/reload-plugins`.
