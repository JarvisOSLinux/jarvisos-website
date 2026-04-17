# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# JARVIS OS Website — Claude Context

## Project overview
This is the official website for **JARVIS OS**, an Arch Linux-based AI-native operating system
built as a research project at **Washington State University (WSU Everett)** by Yakup and
co-author Toufic Majdaleni, under a WSU research grant. The project is being presented at
**SURCA** (Showcase for Undergraduate Research and Creative Activities) and is heading toward
a full academic paper.

The website lives at the **`jarvisos` GitHub organization** and is hosted via GitHub Pages.

---

## What JARVIS OS is
- Arch Linux base + KDE Plasma 6 on Wayland
- **Ollama** for local LLM inference (no cloud dependencies)
- **MCP (Model Context Protocol)** orchestration layer — lets the LLM autonomously create,
  modify, and delete system tools with sudo access
- Built to empirically study security threats that emerge when LLMs gain OS-level privileges
- Seven-script modular build pipeline that transforms a base Arch ISO into a bootable AI-native OS
- Calamares installer for permanent installation

## Research core — the seven-threat taxonomy
Derived from direct experience building and operating JARVIS OS:
1. Misinterpreted MCP keyword search
2. Misleading MCP server usage
3. Unverified community MCP servers
4. Unauthorized sudo requests via MCP
5. Sudo capability exploitation
6. Unintended file modification/deletion
7. **Forgetful context** — LLMs silently drop previously stated security constraints
   mid-session. **Novel finding with no prior literature.** Always emphasize this one.

Three privilege escalation tiers studied: (1) sandboxed/user-level, (2) sudo/elevated,
(3) web-enabled.

---

## Website tech stack
- **Astro** — static site framework, pages as `.astro` files
- **GitHub Pages** deployment via `jarvisos.github.io`
- **GitHub API** — used for live data: releases, contributors, repo stats
- No backend, no build-time secrets required
- Styling: custom CSS (dark, industrial, cyan accent aesthetic — see `index.html` for the
  design language already established)

## Design language (already established)
- Dark theme: `#07090f` background, `#00c8ff` cyan accent, `#f0a500` gold for warnings
- Fonts: `Chakra Petch` (headings), `DM Mono` (code/labels), `DM Sans` (body)
- Grid overlay background, scan line animations, bordered cards
- Severity labels: Critical (red), High (gold), Medium (cyan), Novel (green)
- Tone: technical, research-credible, not marketing fluff

---

## Site structure — pages to build
```
/               Home — hero, features overview, architecture diagram, download CTA
/download       Releases, ISO download, SHA-512 checksum, mirrors, install instructions
/subsystems     Each component of the project explained, links to their GitHub repos
/research       Threat taxonomy (all 7), SURCA poster, paper abstract, methodology
/docs           Getting started, build system walkthrough (the 7 scripts explained)
/contributors   Team (Yakup + Toufic) + GitHub API contributors list
```

## Subsystems to showcase on /subsystems
Each subsystem should link to its own repo under the `jarvisos` GitHub org:
- **jarvis-core** — the AI daemon and CLI (`jarvis` command)
- **mcp-servers** — the MCP orchestration layer and tool registry
- **calamares-config** — the Calamares installer configuration package
- **jarvisos** (main) — the 7-script build pipeline (01 through 07 scripts)

---

## Key framing notes (important for copy/content)
- The project's academic contribution is the **platform + threat taxonomy**, not just the software
- JARVIS OS **outperforms OpenClaw** in context preservation and task management
- Traditional OS security models are **inadequate for probabilistic AI agents** — this is the
  central thesis
- "Forgetful context" (threat #7) is the standout novel finding — lead with it in research contexts
- The project is accurately described as **ongoing** — empirical results are forthcoming
- Framing: research platform first, cool Linux distro second

---

## GitHub org structure
```
github.com/jarvisos/
├── jarvisos            ← main build system (the 7 scripts)
├── jarvis-core         ← AI daemon + CLI
├── mcp-servers         ← MCP orchestration layer
├── calamares-config    ← installer config
└── jarvisos.github.io  ← this website
```

## Conventions
- Keep pages modular — shared nav and footer as Astro components
- GitHub API data (releases, contributors) is fetched at **runtime via client-side JS**, not at build time — see `download.astro` and `contributors.astro` for the pattern
- Contributors page fetches from GitHub API (don't hardcode contributor lists)
- All internal links use Astro's file-based routing
- Use the `@/` path alias (maps to `src/`) for all internal imports

---

## Development commands

```bash
npm install       # install dependencies (Astro 5.x is the only dep)
npm run dev       # start dev server at http://localhost:4321
npm run build     # build static output to dist/
npm run preview   # preview the built output locally
```

No linter or formatter is configured. TypeScript is set to strict mode via `astro/tsconfigs/strict`.

---

## Architecture

**Every page** wraps with `src/layouts/BaseLayout.astro`, which injects `<Nav>`, the page slot, and `<Footer>`. Pass `title` (required) and `description` (optional) as props.

**Shared components** live in `src/components/`:
- `SubsystemCard.astro` — props: `name`, `repo`, `description`, `tags[]`
- `ThreatCard.astro` — props: `number`, `title`, `severity`, `description`
- Severity values: `"critical"` | `"high"` | `"medium"` | `"novel"`

**Global styles** are in `src/styles/global.css`. Use the existing utility classes (`.container`, `.section`, `.card`, `.btn-primary`, `.btn-outline`, `.grid-2/3/4`, `.tag-*`) rather than adding inline styles. The 768 px breakpoint is the only responsive breakpoint.

**GitHub API fetching** in `download.astro` and `contributors.astro` is plain `fetch()` in a `<script>` tag that runs in the browser — there is no server and no build-time secret handling.
