---
name: create-design-system
description: Use when the user wants to extract a design system from any public website and generate a DESIGN.md folder in this repo. Triggers on "create design system", "extract design system", "generate DESIGN.md", "add a new site to awesome-design-md", or any request to scrape/document a site's visual language. Runs interactively by default (prompts for URL, folder name, theme mode) OR non-interactively when a URL is passed as an argument. Produces the canonical 4-file folder matching design-md/claude/ and design-md/indianic/ — DESIGN.md (9 sections), README.md, preview.html, preview-dark.html.
---

# create-design-system

Extract the visual language of any public website and generate a production-ready `design-md/<slug>/` folder in this repo, matching the exact structure of the existing `design-md/claude/` and `design-md/indianic/` references.

## When to use this skill

- User says: "create a design system for <url>", "extract design system from <url>", "add <brand> to awesome-design-md", "generate DESIGN.md for <site>"
- User asks to document a site's colors/typography/components in the repo format
- User wants to scaffold a new entry under `design-md/`

## Inputs

This skill accepts **one optional argument**: a URL.

- **With URL argument** (`/create-design-system https://stripe.com`) → run non-interactively. Auto-slug the folder from the domain, generate both light and dark previews, no prompts.
- **Without argument** → run interactively. Use `AskUserQuestion` to gather: URL, folder name (with auto-suggested default), theme mode.

## Workflow

### Step 1 — Gather inputs

**Interactive mode** (no URL passed):
Ask three questions using `AskUserQuestion`. Batch them into a single tool call for efficiency:

1. **URL** (open-ended text) — "Which website should I extract a design system from? Enter the full URL."
2. **Folder name** — after computing the auto-slug from the URL's domain, ask: "I'll use `<slug>` as the folder name under `design-md/`. Accept or override?" (Options: "Use <slug>", "Override with custom name")
   - If "Override", ask a follow-up open-ended question for the custom name.
3. **Theme mode** — "Which preview themes should I generate?" (Options: "Both light and dark (recommended)", "Light only", "Dark only")

**Non-interactive mode** (URL passed as argument):
- Slug = domain without `www.` and TLD (e.g., `https://www.stripe.com/` → `stripe`; `https://linear.app` → `linear.app` if the TLD is part of the brand — match the existing repo convention by checking `ls design-md/` for similar entries).
- Theme mode = both.
- Folder name = slug.

**Slug derivation rules** (match existing repo conventions):
- Strip `https://`, `http://`, `www.`, and trailing slashes
- Take the hostname, lowercase it
- For multi-word brands, keep the dot if the existing repo uses it (e.g., `linear.app`, `mistral.ai`, `opencode.ai`, `together.ai`, `x.ai`) — check `design-md/` listing first
- Otherwise strip the TLD (e.g., `stripe.com` → `stripe`, `indianic.com` → `indianic`)
- Before creating the folder, check `design-md/<slug>/` does not already exist. If it does, ask the user whether to overwrite or pick a new name.

### Step 2 — Research the site

Use `WebFetch` in **parallel** (single message, multiple tool calls) to gather design tokens:

1. **Homepage** — ask for: brand colors with hex codes, typography (fonts, sizes, weights), button styles, hero section, navigation, layout patterns, overall mood, primary CTA color, background colors, text colors, what the company does.
2. **About page** (`/about`, `/about-us`, `/company`) — ask for: unique colors beyond primary, illustration/photography style, typography hierarchy, decorative elements, brand voice.
3. **Services/Products/Pricing page** — ask for: card styles, icon styles, layout patterns, CTA placement, any distinctive components.

If a page 404s or is behind auth, skip it and rely on the homepage. Never fail the whole workflow because a secondary page is missing.

#### Fallback: Jina Reader (when WebFetch is blocked)

Some sites (Zomato, Cloudflare-protected sites, heavy-SPA apps, bot-blocked hosts) will time out or return empty content from `WebFetch`. **Do not retry the same URL more than once** — if a `WebFetch` call fails, errors, or returns obviously empty/challenge-page content, immediately fall back to **Jina Reader**, a free no-auth service that proxies the URL through a real headless browser and returns clean markdown.

**How to use Jina Reader as a fallback:**

Prefix the original URL with `https://r.jina.ai/` and call `WebFetch` again with the same prompt. Example:

- Original (failed): `https://www.zomato.com/about`
- Fallback: `https://r.jina.ai/https://www.zomato.com/about`

```
WebFetch(
  url: "https://r.jina.ai/https://www.zomato.com/about",
  prompt: "<same prompt as the original call>"
)
```

**Fallback rules:**
- Trigger the fallback on: timeout, network error, 403/429/503 response, or content that is clearly a bot-challenge / empty shell.
- Only retry through Jina **once per URL**. If Jina also fails, mark that page as unreachable and continue with what you have.
- Never retry the same bare URL 3+ times — that wastes context and time. One direct attempt → one Jina attempt → move on.
- When you use the Jina fallback, briefly tell the user (e.g., "zomato.com blocked direct fetch — using Jina Reader proxy").

If **every** page (direct + Jina fallback) is unreachable, stop and ask the user whether to proceed with a best-guess design based on the brand name (clearly marked as speculative in the DESIGN.md).

### Step 3 — Synthesize the design direction

Before writing any files, decide on:
- **One "signature move"** — the single most distinctive thing about the site (e.g., Claude's terracotta-on-parchment warmth, Stripe's purple gradients, Linear's tight type)
- **A single brand accent color** that will be rationed across the design
- **A primary font family** and how hierarchy is expressed (weight vs. size vs. family switching)
- **Whether the site is predominantly light, dark, or alternating**
- **Shadow philosophy** (ring shadows, drop shadows, tinted, none)

Commit to bold, specific choices. Avoid generic AI-slop ("modern, clean, minimalist" with no specifics).

### Step 4 — Create the folder and files

Create `design-md/<slug>/` with exactly these four files. Use the templates below as scaffolds, then fill in site-specific content.

#### File 1 — `DESIGN.md`

Follow the 9-section Stitch format **exactly**. Every section is required. Use the `design-md/claude/DESIGN.md` and `design-md/indianic/DESIGN.md` files as ground-truth references for tone, depth, and specificity.

```markdown
# Design System Inspiration of <Brand>

## 1. Visual Theme & Atmosphere
<2–3 paragraphs. Lead with the signature move. Describe the mood, the canvas, the brand accent, and what makes this site unforgettable. Use concrete hex values inline.>

**Key Characteristics:**
- <5–7 bullets capturing the distinctive elements>

## 2. Color Palette & Roles
### Primary
- **<Semantic Name>** (`#hex`): <role and reasoning>
### Secondary & Accent
### Surface & Background
### Neutrals & Text
### Semantic & Borders
### Gradient System
<List every gradient with its stops. If the site has no gradients, say so explicitly.>

## 3. Typography Rules
### Font Family
### Hierarchy
<Full markdown table: Role | Font | Size | Weight | Line Height | Letter Spacing | Notes>
### Principles
<5–6 bullets on weight strategy, line-height choices, letter-spacing, serif/sans split, etc.>

## 4. Component Stylings
### Buttons
<Every button variant with background, text, padding, radius, shadow, hover state>
### Cards & Containers
### Inputs & Forms
### Navigation
### Image Treatment
### Distinctive Components
<Site-specific components that don't fit standard patterns — stat chips, logo carousels, sticky widgets, etc.>

## 5. Layout Principles
### Spacing System
### Grid & Container
### Whitespace Philosophy
### Border Radius Scale

## 6. Depth & Elevation
<Markdown table: Level | Treatment | Use>
**Shadow Philosophy**: <1 paragraph explaining how depth is communicated>
### Decorative Depth

## 7. Do's and Don'ts
### Do
### Don't

## 8. Responsive Behavior
### Breakpoints
### Touch Targets
### Collapsing Strategy
### Image Behavior

## 9. Agent Prompt Guide
### Quick Color Reference
### Example Component Prompts
<5 concrete prompts an agent can paste into Claude/Cursor/Stitch>
### Iteration Guide
```

#### File 2 — `README.md`

Use this exact template, substituting `<brand>`, `<slug>`, and the one-line description:

```markdown
# <Brand> Inspired Design System

[DESIGN.md](https://github.com/VoltAgent/awesome-design-md/blob/main/design-md/<slug>/DESIGN.md) extracted from the public [<Brand>](<url>) website. This is not the official design system. Colors, fonts, and spacing may not be 100% accurate. But it's a good starting point for building something similar.

## Files

| File | Description |
|------|-------------|
| `DESIGN.md` | Complete design system documentation (9 sections) |
| `preview.html` | Interactive design token catalog (light) |
| `preview-dark.html` | Interactive design token catalog (dark) |


Use [DESIGN.md](https://github.com/VoltAgent/awesome-design-md/blob/main/design-md/<slug>/DESIGN.md) as a reference for AI agents (Claude, Cursor, Stitch) to generate UI that looks like the <Brand> design language — <one-sentence signature-move summary>.

## Preview

A sample landing page built with DESIGN.md. It shows the actual colors, typography, buttons, cards, spacing, and elevation, all in one page. Open `preview.html` for the light theme and `preview-dark.html` for the dark theme.
```

#### File 3 — `preview.html` (light)

A self-contained HTML file demonstrating every token from the DESIGN.md. Required sections in order:
1. **Nav** — sticky top bar with brand logo mark, anchor links, a **theme toggle pill** linking to `preview-dark.html`, and a primary CTA button
2. **Hero** — eyebrow label + headline + subheadline + 2 buttons
3. **Colors** — grouped swatches (Primary, Accent, Surface, Neutrals, Borders) with name, hex, and role
4. **Typography** — every role in the DESIGN.md hierarchy table, with live rendering and spec label
5. **Buttons** — every variant with hover states
6. **Cards** — at least 3 card variants (standard, hover-lift, featured)
7. **Statistics** — if the brand uses stats, reproduce them; otherwise skip
8. **Spacing** — visual ladder of spacing scale
9. **Radius** — visual ladder of radius scale
10. **Elevation** — visual ladder of shadow levels
11. **Forms** — at least 4 input fields with label, input, helper text, focus state
12. **Footer** — dark band with brand mark and copy
13. **Sticky widget** — if the brand has one (chat bubble, discussion pill, CTA tab)

**Theme toggle link** must appear in the nav. Structure:
```html
<a href="preview-dark.html" class="theme-toggle" title="Switch to dark mode">🌙 Dark</a>
```
Style it as a pill matching the brand, border tinted with the brand accent on hover.

Use Google Fonts via `<link rel="stylesheet">` for any non-system fonts. Use CSS custom properties (`--color-*`, `--font-*`) in `:root`. Use `clamp()` for fluid typography.

Use `design-md/indianic/preview.html` as the reference implementation — match its section structure, CSS organization, responsive breakpoints, and accessibility patterns.

#### File 4 — `preview-dark.html`

Identical structure to `preview.html` but with dark tokens. The theme toggle in the nav links back to `preview.html` with label "☀️ Light". Keep the brand accent the same — only surfaces, text, and borders shift.

If the user chose "Light only" or "Dark only" in Step 1, skip the corresponding file but still include the theme toggle link pointing at the other file. In that case, warn the user that the toggle will 404 until the other file is generated.

### Step 5 — Verify

After writing all files:

1. Run `ls -la design-md/<slug>/` to confirm all four files exist
2. Parse both HTML files with Python's `html.parser.HTMLParser` to check for unclosed tags:
   ```bash
   python3 -c "
   from html.parser import HTMLParser
   for f in ['design-md/<slug>/preview.html','design-md/<slug>/preview-dark.html']:
       class V(HTMLParser):
           def __init__(s): super().__init__(); s.stack=[]
           def handle_starttag(s,t,a):
               if t not in ('br','img','input','meta','link','hr'): s.stack.append(t)
           def handle_endtag(s,t):
               if s.stack and s.stack[-1]==t: s.stack.pop()
       v=V(); v.feed(open(f).read())
       print(f, 'unclosed:', v.stack or 'none')
   "
   ```
3. Report line counts of each file
4. Report to the user: files created, signature design direction chosen, and an optional next step ("add this to the root README.md collection list under category X").

### Step 6 — Offer README update

Ask the user whether to also update the root `README.md` collection list:
- Increment the badge count (`![DESIGN.md Count](https://img.shields.io/badge/DESIGN.md%20count-N-10b981...)`)
- Add a bullet under the appropriate category section (AI & Machine Learning, Developer Tools & Platforms, Infrastructure & Cloud, Design & Productivity, Fintech & Crypto, Enterprise & Consumer, Car Brands)

Do not update the root README automatically — this is a separate user decision.

## Quality bar

- **No generic AI slop**: Avoid "modern, clean, minimalist, professional" without specifics. Every claim must be backed by a hex code, a font name, a pixel value, or a concrete component description.
- **Warm vs cool discipline**: If the site has warm neutrals, every gray must be warm. If cool, every gray must be cool. No mixing.
- **Ration the brand color**: Primary brand colors appear on CTAs and small accents only, never as section backgrounds (unless the site genuinely uses them that way — e.g., Claude's dark sections).
- **Specificity**: Name every color (e.g., "Terracotta Brand" not "red"). Give every shadow a rgba value.
- **Match the reference depth**: The indianic and claude DESIGN.md files are ~300+ lines. Don't ship a 50-line stub.

## Anti-patterns to avoid

- Do not use `Inter`, `Roboto`, or `system-ui` unless the target site actually uses them
- Do not invent gradients that aren't on the site
- Do not copy the indianic or claude color palette — research fresh for every site
- Do not skip the theme toggle link in the nav
- Do not write `# TODO` or placeholder text in DESIGN.md
- Do not create more than 4 files in the folder

## Reference implementations

When in doubt, read these files before generating:
- `design-md/claude/DESIGN.md` — warm, editorial, serif-driven, alternating light/dark sections
- `design-md/claude/preview.html` — reference nav + hero + color grid structure
- `design-md/indianic/DESIGN.md` — confident enterprise, rationed red accent, Poppins
- `design-md/indianic/preview.html` — reference for stats row, sticky widget, form grid, theme toggle implementation
- `CONTRIBUTING.md` — contribution guidelines for the repo
- Root `README.md` — collection category structure

## Dependencies

This skill uses only built-in Claude Code tools:
- `WebFetch` — for scraping the target site
- `AskUserQuestion` — for interactive prompts
- `Write` — for file creation
- `Bash` — for directory creation and HTML validation
- `Read` / `Glob` — for reading reference implementations

**No MCP server is required.** WebFetch handles all network access, with `https://r.jina.ai/<url>` as a free no-auth proxy fallback for bot-blocked sites. If the user specifically wants an MCP server for this, explain that it would add install complexity without functional benefit — the skill is the right primitive.
