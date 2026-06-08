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

## Research core — the six-threat taxonomy

Empirically identified through building and operating JARVIS OS. NOT seven — six.
See `docs/RESEARCH-SPEC.md` for the canonical reference.

| # | Threat | Primary Mitigation |
|---|--------|--------------------|
| 1 | Malicious MCP Servers | Community-vetted AUR-style registry |
| 2 | Prompt Injection | Cryptographic Boundary Protocol |
| 3 | Misleading MCP Server Usage | Registry vetting + structured tool schema |
| 4 | Unauthorized Sudo Requests via MCP | TLA system + PolicyKit enforcement |
| 5 | Sudo Capability Exploitation | TLA + goal-scoped confirmation |
| 6 | Bloated Context | dispatch rolling window + contextor pruning |

**Key framing changes from earlier drafts:**
- "Forgetful Context" is now **"Bloated Context"** — reframed as context window
  saturation as a **discrete security threat**, not a reliability problem.
- "Misinterpreted MCP Keyword Search" is subsumed by "Misleading MCP Server Usage."
- "Unintended File Modification/Deletion" is a consequence of threats 4+5, not a root cause.
- **Prompt Injection** is now an explicit threat in the taxonomy.

Three privilege escalation stages: (1) user-level, (2) sudo-enabled, (3) web-enabled.

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
/research       Threat taxonomy (all 6), SURCA poster, paper abstract, methodology
/docs           Getting started, build system walkthrough (the 7 scripts explained)
/contributors   Team (Yakup + Toufic) + GitHub API contributors list
```

## Subsystems to showcase on /subsystems
Each subsystem links to its repo under the `JarvisOSLinux` GitHub org:
- **Project-JARVIS** — AI assistant system (Python daemon, LLM orchestration, TUI)
- **dispatch** — Rust signal-driven parallel task orchestrator ("one brain, many hands")
- **dmcp** — Rust MCP server lifecycle manager (dual-scope: user/system)
- **contextor** — Rust persistent memory store (vector search, session summaries)
- **mcp-registry** — community-vetted MCP server catalog
- **jarvisos** — AI-native Linux distro (7-script build pipeline)
- **jarvisos-app** — desktop GUI widget (Rust + CXX-Qt + Qt6/QML)
- **linux-jarvisos** — custom kernel with JARVIS character device drivers

---

## Key framing notes (important for copy/content)
- The project's academic contribution is the **platform + taxonomy + mitigations**, not just the software
- Traditional OS security models are **inadequate for probabilistic AI agents** — this is the
  central thesis
- Do NOT reference "7 threats" or "seven-threat taxonomy" anywhere — it is six
- Do NOT lead with "Forgetful Context" — use **"Bloated Context"** and frame it as a security
  threat, not a curiosity
- **Bloated Context** is the standout novel finding — first identification of context window
  saturation as a discrete security threat rather than a reliability problem
- The project is research-first, product-second
- "Built for people, not corporations."
- Open-source is a structural necessity, not a preference
- SURCA 2026 poster presentation — **Winner, Gray Grant**

---

## GitHub org structure
```
github.com/JarvisOSLinux/
├── jarvisos              ← AI-native Linux distro (Arch base + kernel + build pipeline)
├── Project-JARVIS        ← AI assistant system (Python daemon + LLM orchestration)
├── dispatch              ← Rust signal-driven task orchestrator
├── dmcp                  ← Rust MCP server lifecycle manager
├── contextor             ← Rust vector-based memory store
├── mcp-registry          ← JSON registry of installable MCP servers
├── jarvisos-app          ← Desktop GUI (Rust + Qt6/QML)
├── linux-jarvisos        ← Custom kernel with /dev/jarvis drivers
└── jarvisos-website      ← this website
```

## Canonical content specs
- `docs/RESEARCH-SPEC.md` — single source of truth for all research content on the site
- `docs/EASTER-EGG-SPEC.md` — content spec for `/freedom-control` and `/AI-control` pages

## Conventions
- Keep pages modular — shared nav and footer as Astro components
- Pull release data from GitHub API at build time so download page always shows latest ISO
- Contributors page fetches from GitHub API (don't hardcode contributor lists)
- All internal links use Astro's file-based routing
- Docs pages written in Markdown (`.md`) inside `src/content/docs/`
