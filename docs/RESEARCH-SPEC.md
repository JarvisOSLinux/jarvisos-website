# Research Spec — Canonical Reference for Website Content

This document is the single source of truth for all research-related content
on the JarvisOS website. All pages that reference the research (homepage,
/research, /AI-control, /freedom-control) must align with this spec.

Last updated from: the pre-publication manuscript, June 2026.

---

## Paper Title

**Security Threats in AI-Native Operating Systems: An Empirical Study Using
Privilege-Escalated LLM Agents**

Yakup Atahanov, Toufic Majdalani — Washington State University Everett
Faculty Advisor: Dr. Jeremy Thompson

---

## Core Thesis

Traditional OS security models assume deterministic software. An LLM agent
violates that assumption fundamentally — its behavior is probabilistic,
context-dependent, and shaped by natural language inputs that are difficult
to sanitize or predict.

We chose an operating system as the research environment because it represents
the broadest possible integration surface. If we can characterize and mitigate
threats at the OS level, the findings generalize to every narrower context.

---

## The Six-Threat Taxonomy

NOT seven. NOT "forgetful context" as the headline. The taxonomy is six
empirically-identified threats:

| # | Threat | Escalation Stage | Primary Mitigation |
|---|--------|-----------------|-------------------|
| 1 | Malicious MCP Servers | User / Sudo / Web | Community-vetted AUR-style registry |
| 2 | Prompt Injection | User / Sudo / Web | Cryptographic Boundary Protocol |
| 3 | Misleading MCP Server Usage | User / Sudo / Web | Registry vetting + structured tool schema |
| 4 | Unauthorized Sudo Requests via MCP | Sudo / Web | TLA system + PolicyKit enforcement |
| 5 | Sudo Capability Exploitation | Sudo / Web | TLA + goal-scoped confirmation |
| 6 | Bloated Context | User / Sudo / Web | dispatch rolling window + contextor pruning |

### Key change from old framing

- "Forgetful Context" is now **"Bloated Context"** — reframed as the first
  identification of context window saturation as a **discrete security threat**
  rather than a reliability problem.
- "Misinterpreted MCP Keyword Search" is no longer a separate threat — it is
  subsumed by "Misleading MCP Server Usage" and addressed by the adaptive
  search strategy in dmcp.
- "Unintended File Modification/Deletion" is no longer a separate threat — it
  is a consequence of threats 4 and 5 (unauthorized/exploited sudo), not a
  root cause.
- **Prompt Injection** is now an explicit threat in the taxonomy.

---

## Architectural Mitigations

### Cryptographic Boundary Protocol
dispatch generates a six-character provenance nonce (Splitmix64: task PID +
wall-clock nanoseconds + atomic session counter) for each completed MCP task.
Successful output is stored out-of-band — it never enters the LLM's context
unless explicitly retrieved via `get_output`. This structurally separates the
instruction plane from the data plane.

### TLA (Threat Level Access) System
Dynamic, context-aware privilege model: Guest → User → Elevated → Sudo → Kernel.
Enforced at the OS level. Every tool invocation is evaluated against the current
TLA level. Escalation requires explicit out-of-band user confirmation — it cannot
be triggered by model output or MCP server response alone. Sudo access is scoped
to the current goal and expires on completion.

### Community-Vetted MCP Registry
AUR-style proofread model. Third-party MCP servers pass community review (code,
declared capabilities, tool description accuracy) before being listed. Malicious
or deceptive servers are filtered before they are discoverable by tool search.

### Bloated Context Mitigation
dispatch's bounded rolling signal window (last 20 entries per wakeup) +
contextor's retention-based pruning + high-priority preservation of
security-critical constraints across context refreshes.

---

## Research Methodology — Three Escalation Stages

1. **User-level** — standard access, no sudo. Baseline threat surface.
2. **Sudo-enabled** — full root control. LLM can modify anything.
3. **Web-enabled** — sudo + internet. Enables exfiltration and remote injection.

---

## Four Contributions

1. Six-threat taxonomy for privilege-escalated LLM agents — including Bloated
   Context as the first identification of context saturation as a security threat.
2. Architectural mitigations for each threat class, implemented in JarvisOS.
3. JarvisOS itself — a fully functional, bootable, open-source AI-native OS.
4. MCP tool-description architecture independently developed Oct–Nov 2025,
   predating commercial deployments.

---

## What We Built (for /subsystems and architecture references)

- **dispatch** — signal-driven parallel task orchestrator. "One brain, many
  hands." LLM dispatches and returns to conversation; dispatch wakes LLM only
  on signals.
- **dmcp** — MCP server lifecycle manager. Discovery, install, config, invoke,
  remove. Dual scope (user/system). Also runs as MCP server itself.
- **contextor** — persistent memory backend. Vector similarity search, rolling
  session summaries, retention-based pruning.
- **Seven-script build pipeline** — transforms base Arch ISO into bootable
  AI-native OS with KDE Plasma 6 on Wayland.

---

## Publications

- **SURCA 2026** — Poster, WSU Everett. **Winner, Gray Grant.**
- **Full paper** — pre-publication manuscript available on request.

---

## Framing Rules for Website Copy

- The research contribution is the **platform + taxonomy + mitigations**, not
  just the software.
- Do NOT reference "7 threats" or "seven-threat taxonomy" anywhere.
- Do NOT lead with "Forgetful Context" as a novel finding — use "Bloated
  Context" and frame it as a security threat, not a curiosity.
- The project is research-first, product-second.
- "Built for people, not corporations."
- Open-source is a structural necessity, not a preference.
