<div align="center">

# Agent Setup

*A portable toolkit for coding agents — Claude Code and Codex on Windows, driven through Zed*

[Overview](#overview) • [Toolbox](#toolbox) • [Workflow](#workflow) • [Gotchas](#gotchas)

</div>

This repository is a **field kit**: the plugins, skills and MCP servers I carry between machines, documented so Claude Code and Codex share one identical setup and work swaps sides without re-learning anything.

> [!NOTE]
> A snapshot of one machine — not an installer or dotfiles.  
> Last reviewed: **2026-09-18**

## Overview

| | Claude Code | Codex |
|---|---|---|
| CLI | `2.1.198` | `codex-cli 0.153.4` |
| Provider / Model | Z.ai GLM: `glm-5.3-flash`, effort `max` | `gpt-5.6-luna`, reasoning `xhigh` |
| Main config | `~/.claude/settings.json`, `~/.claude.json` | `~/.codex/config.toml` |

Check current state:

```bash
claude --version
codex --version
claude plugin list
codex plugin list
claude mcp list
codex mcp list
```

## Toolbox

### Plugins

| Plugin | Scope | What it does |
|---|---|---|
| [`caveman`][cav] | Claude + Codex | Terse replies plus focused workflows: `investigate-first`, `surgical-patch`, `safe-refactor`, `migration`, `lean-build`, `verify-and-stop` |
| [`ponytail`][pony] | Claude + Codex | Forces the shortest working path: YAGNI, stdlib first, no over-engineering |
| [`mattpocock-skills`][mp] | Claude + Codex | `grilling`, `grill-with-docs`, `tdd`, `code-review`, `research`, `prototype`, `handoff`, `to-spec` / `to-tickets`, `wayfinder`, `improve-codebase-architecture`, `wizard`, `triage`, `domain-modeling` |
| [`ecc`][ecc] | Claude + Codex | The full agent-harness system v2.2.1: ~280 skills, ~88 agents (`ecc:planner`, `ecc:tdd-guide`, `ecc:code-reviewer`, per-language reviewers, GAN harness), commands (`/ecc:pr`, `/ecc:build-fix`, `/ecc:learn`), and lifecycle hooks |

```bash
claude plugin update <name>@<marketplace>
```

> [!NOTE]
> Skill names in this doc are shown bare for readability — the link after each name tells you where it lives. When invoking for real: plugin skills need their plugin prefix (`caveman:`, `mattpocock-skills:`, `ponytail:`, `ecc:`); standalone skills are called by bare name.

> [!WARNING]
> **History — why ECC went full-bundle.** The first attempt installed `ecc@ecc` alongside 13 cherry-picked ECC standalone skills: skills budget overflowed, the system stripped skill descriptions and routing broke. The reset wiped every standalone skill and the stale plugin caches first, then installed the plugin alone — that combination holds. Lesson: the budget pays for the *whole* load, so adding ECC means not stacking duplicate skills from the same source.

### Standalone skills

Global store at `~/.agents/skills/` tracked by `.skill-lock.json`. Claude uses symlinks from `~/.claude/skills/`; Codex reads the store directly.

| Skill | Source | Use when |
|---|---|---|
| `create-readme` | [github/awesome-copilot][gh] | Generate a project README |
| `karpathy-guidelines` | [multica-ai/andrej-karpathy-skills][karp] | Keep code simple, readable and verifiable — standing style guard |

Only what ECC does not ship. Everything else (orch pipelines, santa-method, security-review, ADRs, learning, strategic-compact, github-ops equivalents) now arrives via the `ecc:` plugin namespace.

```bash
npx skills add <owner>/<repo> --skill <name> -g
npx skills update
```

### MCP servers

Both agents share the same set:

| Server | Command | What it does | Auth |
|---|---|---|---|
| [`codebase-memory-mcp`][cbm] | local executable | Indexes code into a graph — structure search, call tracing, impact analysis | — |
| [`firecrawl`][fc] | `npx -y firecrawl-mcp` | Web search, scraping, crawling | `FIRECRAWL_API_KEY` |
| [`context7`][c7] | `npx -y @upstash/context7-mcp` | Version-matched library documentation | `CONTEXT7_API_KEY` |
| [`playwright`][pw] | `npx -y @playwright/mcp@latest --browser msedge` | Browser control, E2E, screenshots | — |

> [!TIP]
> Reach for [`codebase-memory-mcp`][cbm] before `grep` when hunting symbols, callers or architecture. Keep `grep` for literals, config and files the graph does not cover.

Add an MCP server on the Codex side:

```bash
codex mcp add <name> --env KEY=VALUE -- <command>
```

## Workflow

### 1. Pick a lane before touching code

| Situation | Start with |
|---|---|
| Requirements unclear | [`grilling`][mp] — walk decisions as a design tree; [`grill-with-docs`][mp] when ADRs/glossary should be written along the way |
| Big idea, real risk | [`product-capability`][ecc] to expose constraints → record an ADR |
| Unsure it is feasible | [`prototype`][mp] — throwaway check before committing |
| Facts / comparisons needed before deciding | [`research`][mp] — evidence-first with citations |
| Discussion settled, need the artifact | [`to-spec`][mp] (spec) · [`to-tickets`][mp] (blocker-linked tickets) |
| Feasibility settled | Enter a pipeline below |

### 2. Pick the pipeline that matches the job

| Job | Pipeline |
|---|---|
| New feature (medium+) | [`orch-add-feature`][ecc] — gated Research-Plan-TDD-Review-Commit, phases delegated to `ecc:` agents |
| Change an existing feature | [`orch-change-feature`][ecc] |
| Small bug / cause already narrow | [`investigate-first`][cav] then [`surgical-patch`][cav] |
| Critical or recurring bug | [`orch-fix-defect`][ecc] (regression test included) |
| Build broken | [`build-fix`][ecc] — routes to the per-language build resolver agent |
| Behavior-preserving refactor | [`safe-refactor`][cav] |
| Feature at risk of scope creep | [`lean-build`][cav] |
| Migration needing rollback | [`migration`][cav] |
| Work too big for one session | [`wayfinder`][mp] — decision-ticket map, resolve one at a time |
| Issue / PR backlog piling up | [`triage`][mp] · [`github-ops`][ecc] for `gh` operations |
| Designing a module's interface or seam | [`codebase-design`][mp] · [`domain-modeling`][mp] (CONTEXT.md + domain model) |
| Setup only a human can do (CI secrets, provisioning) | [`wizard`][mp] — generates a guided walk-through |
| Tiny task | Do it directly, [`ponytail`][pony] keeps scope honest |

> [!TIP]
> For medium-and-up work let the `orch-*` pipeline drive. Do not call [`tdd`][mp] separately — the pipeline already runs test-first via `ecc:tdd-guide`.

### 3. While working (every lane)

- Structure, callers, impact → [`codebase-memory-mcp`][cbm] before `grep`
- Version-sensitive APIs → [`context7`][c7] before writing
- Long session → [`strategic-compact`][ecc] at phase boundaries
- Significant design decision → [`architecture-decision-records`][ecc] immediately
- Merge/rebase conflict in progress → [`resolving-merge-conflicts`][mp]
- A concept to learn properly → [`teach`][mp]

### 4. Before shipping

1. [`code-review`][mp] on every diff
2. [`ponytail-review`][pony] when over-engineering is a risk
3. [`santa-method`][ecc] for critical work
4. [`security-review`][ecc] when touching auth, input or secrets
5. [`verify-and-stop`][cav] as the final gate
6. [`pr`][ecc] — templated PR from unpushed commits

### 5. End of session

- [`handoff`][mp] — compress the conversation into a handoff doc another agent (or Codex) can pick up
- [`learn`][ecc] captures patterns as instincts (hooks now auto-register via the plugin)

### 6. The rest of the kit (situational)

| Skill | When |
|---|---|
| [`improve-codebase-architecture`][mp] | Periodic deepening scan → HTML report → grill the pick |
| [`diagnosing-bugs`][mp] | Alternative bug loop — pick this or [`investigate-first`][cav], not both |
| [`ponytail-audit`][pony] | Whole-repo over-engineering audit, one-shot report |
| [`create-readme`][gh] | New repo needs a README |
| [`karpathy-guidelines`][karp] | Standing style guard — always on, no invocation needed |
| [`ask-matt`][mp] | Router — "which of these skills fits my situation?" |
| [`wait-what`][mp] | Last answer did not land — force a re-pitch |
| [`to-questionnaire`][mp] | Turn an unanswerable decision into a questionnaire |
| [`writing-for-agents`][mp] | When writing or editing skills / AGENTS.md |
| [`ecc-guide`][ecc] | Tour of everything ECC ships — agents, commands, hooks |

## Repo conventions

These files help agents understand a project the way the team does:

| File | Modelled after | Purpose |
|---|---|---|
| `AGENTS.md` | per repository | Instructions, boundaries and workflow for agents |
| `DESIGN.md` | [google-labs-code/design.md][gdesign] | Design tokens and the reasoning behind UI choices |
| `GLOSSARY.md` | [GoogleCloudPlatform/scion][scion] | Domain vocabulary and team-agreed meanings |

Create only the files a project actually needs, filled with that project's real content.

## Gotchas

1. **One MCP process per session** — close unused Zed tabs to avoid orphaned processes. Codex multi-agent threads leak `codebase-memory-mcp.exe` instances; sweep periodically:

   ```powershell
   Get-CimInstance Win32_Process -Filter "Name = 'codebase-memory-mcp.exe'" |
     Select-Object ProcessId, ParentProcessId, CreationDate
   ```

2. **Skills budget is finite** — the full ECC load fits only because nothing duplicates it. Before adding another big plugin or standalone skill, check the load does not return the `Exceeded skills context budget` warning.
3. **ECC hooks gate Bash** — GateGuard demands a stated purpose before first Bash and before destructive commands. State facts, retry; for repair work `ECC_GATEGUARD=off` exists (use sparingly).
4. **Git Bash rewrites paths** — prefix `MSYS_NO_PATHCONV=1` when an argument starting with `/` must survive intact.
5. **Windows PowerShell 5.1 lacks `&&`** — use `;`, PowerShell 7 or Git Bash.
6. **API keys sit in plaintext** in `~/.claude.json` and `~/.codex/config.toml`.

> [!CAUTION]
> Before sharing any config, strip tokens, API keys, private endpoints and user-identifying paths.

## Key files

```text
~/.claude/settings.json           provider, model mapping, plugins, statusline
~/.claude/rules/                  MCP selection, orchestration, skill routing
~/.claude/projects/<proj>/memory  durable per-project memory
~/.claude/plugins/cache/          installed plugins
~/.claude/skills/learned/         instinct output of ecc:learn
~/.codex/config.toml              Codex plugins, MCP, sandbox and hooks
~/.agents/skills/                 central standalone-skill store
```

## Link definitions

Every external reference in this file is a reference-style link defined here:

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
[gdesign]: https://github.com/google-labs-code/design.md
[scion]: https://github.com/GoogleCloudPlatform/scion/blob/main/GLOSSARY.md
