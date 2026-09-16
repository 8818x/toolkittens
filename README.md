<div align="center">

<img src="assets/toolkittens-logo.png" alt="Toolkit(ten)s" width="220" />

# Toolkit(ten)s

*A portable field kit for coding agents — Claude Code and Codex on one shared setup*

[Overview](#overview) • [Docs](#docs) • [Workflow](#workflow) • [Gotchas](#gotchas)

</div>

Toolkit(ten)s documents one machine's agent setup as a kit you can carry: plugins, hand-picked skills and MCP servers wired identically into **Claude Code** and **Codex** (driven through Zed), so work swaps agents without re-learning anything.

> [!NOTE]
> A snapshot of a real machine — not an installer or dotfiles.  
> Last reviewed: **2026-09-17**

## Overview

| | Claude Code | Codex |
|---|---|---|
| CLI | `2.1.198` | `codex-cli 0.153.4` |
| Provider / Model | Z.ai GLM: `glm-5.3-flash`, effort `max` | `gpt-5.6-luna`, reasoning `xhigh` |
| Main config | `~/.claude/settings.json`, `~/.claude.json` | `~/.codex/config.toml` |

**What rides in the kit**

- **3 plugins** — [caveman][cav] (terse mode + focused fix workflows), [ponytail][pony] (shortest working path), [mattpocock-skills][mp] (grilling, TDD, review, planning)
- **11 standalone skills** — cherry-picked from [affaan-m/ECC][ecc], [awesome-copilot][gh] and [karpathy-skills][karp] via the `skills` CLI
- **4 MCP servers** — [codebase-memory-mcp][cbm] (code graph), [context7][c7] (version-matched docs), [firecrawl][fc] (web research), [playwright][pw] (browser control)
- **1 debug kit** — troubleshooting skills this repo carries in `.claude/skills/`

## Docs

| File | Contents |
|---|---|
| [AGENT-SETUP.md](AGENT-SETUP.md) | The full kit: plugins, skills, MCP servers, config files, gotchas |
| [SYSTEM_DEBUG_KIT.md](SYSTEM_DEBUG_KIT.md) | The repo-carried troubleshooting skills (systematic debugging, Linux/Windows triage) |

## Workflow

The short version — full lanes and pipelines in [AGENT-SETUP.md](AGENT-SETUP.md#workflow):

```
1. Pick a lane      grilling · product-capability · prototype · research
2. Pick a pipeline  orch-add/change/fix · safe-refactor · lean-build · wayfinder
3. While working    codebase-memory before grep · context7 before APIs
4. Before shipping  code-review → santa-method → security-review → verify-and-stop
5. End of session   handoff (→ Codex) · continuous-learning
```

## Gotchas

The machine-specific traps (MCP process leaks, skills budget, Windows path quirks) live in [AGENT-SETUP.md](AGENT-SETUP.md#gotchas).

> [!CAUTION]
> Config files referenced by this kit contain plaintext API keys. Never share `~/.claude.json` or `~/.codex/config.toml`.

## Link definitions

<!-- reference-style link definitions -->
[cav]: https://github.com/JuliusBrussee/caveman
[mp]: https://github.com/mattpocock/skills
[pony]: https://github.com/DietrichGebert/ponytail
[ecc]: https://github.com/affaan-m/ECC
[gh]: https://github.com/github/awesome-copilot
[karp]: https://github.com/multica-ai/andrej-karpathy-skills
[cbm]: https://github.com/DeusData/codebase-memory-mcp
[c7]: https://github.com/upstash/context7
[fc]: https://github.com/firecrawl/firecrawl-mcp
[pw]: https://github.com/microsoft/playwright-mcp
