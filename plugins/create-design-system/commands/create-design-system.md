---
description: Extract a design system from any public URL and generate a design-md/<slug>/ folder with DESIGN.md, README.md, preview.html, and preview-dark.html. Runs interactively if no URL is given.
argument-hint: [url]
---

Invoke the `create-design-system` skill to extract a design system from a website and scaffold the four-file folder under `design-md/`.

**Arguments**: $ARGUMENTS

**Behavior**:
- If `$ARGUMENTS` contains a URL, run non-interactively: auto-slug the folder name from the domain, generate both light and dark preview files, skip all prompts.
- If `$ARGUMENTS` is empty, run interactively: use `AskUserQuestion` to prompt for the URL, confirm the auto-suggested folder name (user can override), and ask which theme previews to generate (both / light only / dark only).

**Follow the full workflow in `.claude/skills/create-design-system/SKILL.md`**:

1. Gather inputs (interactive or non-interactive)
2. Research the site with parallel `WebFetch` calls on homepage, about, and services/pricing pages
3. Synthesize a bold design direction — pick one signature move, one rationed brand accent, a specific font family
4. Write the four files using the 9-section DESIGN.md template and the `design-md/indianic/preview.html` structure as the reference implementation
5. Include the theme toggle link in the nav of both preview files
6. Verify: `ls` the folder and parse both HTML files for unclosed tags
7. Report back with files created, signature design direction, line counts, and offer to update the root `README.md` collection list

**Quality bar**: No generic AI slop. Every claim backed by a hex code, font name, or pixel value. Match the depth of `design-md/claude/DESIGN.md` and `design-md/indianic/DESIGN.md` (~300+ lines). Never copy palettes from existing entries — research fresh for every site.

Read `design-md/claude/DESIGN.md` and `design-md/indianic/DESIGN.md` as ground-truth references before generating.
