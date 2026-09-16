<div align="center">

# System Debug Kit

*Troubleshooting skills this repo carries on the road — drop into any machine, and the agent knows how to think before it fixes.*

</div>

These skills live in `.claude/skills/` and load **only** when a session starts inside this folder. They never touch the global skills budget, which keeps routing sharp everywhere else.

| Skill | Source | Use when |
|---|---|---|
| `systematic-debugging` | [obra/superpowers](https://github.com/obra/superpowers) | The master rule before touching anything: evidence → hypothesis → discriminating test → minimal fix → verify |
| `linux-troubleshooting` | [peterbamuhigire/linux-skills](https://github.com/peterbamuhigire/linux-skills) | Triage a production Linux incident across CPU, memory, disk, services, network |
| `windows-troubleshooting` | Hand-written, following the [Agent Skills spec](https://github.com/agentskills/agentskills) | Diagnose a Windows machine — read-only evidence first, always |

## Layout

```
.claude/skills/
├── systematic-debugging/SKILL.md
├── linux-troubleshooting/SKILL.md
└── windows-troubleshooting/SKILL.md
```

## Adding a skill to the kit

1. Create a folder: `.claude/skills/<name>/`
2. Drop in a `SKILL.md` with frontmatter `name:` and `description:` — the description is the trigger, so write it the way the agent should match work to it
3. Start a new session in this repo — the skill loads automatically

> [!TIP]
> Keep the kit lean. Every `description:` line loads into every session context in this repo — a skill you have not used in months is dead weight in the bag.
