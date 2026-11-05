# my-skills

Compact Claude Code skills. Copy a folder into `.claude/skills/`.

| Skill | Use |
|---|---|
| [super-orchestrator](super-orchestrator/SKILL.md) | Long multi-file coding tasks: orchestrator + workers in git worktrees |

## Recommended external skills (links, not copies)
Install from source to get updates. Install only what you use: every skill description loads into context each session.

| Skill | Does | Fits |
|---|---|---|
| [obra/superpowers](https://github.com/obra/superpowers) | Plan, test-first, systematic debugging | Dev work |
| [anthropics/skills](https://github.com/anthropics/skills) | Official: docx, xlsx, pptx, pdf, skill-creator | Everyone |
| [ui-ux-pro-max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | Design rules, colors, fonts, UX checks | Sites, apps |
| [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) | Avoid generic AI-looking UI | Frontend |
| [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills) | ~50 skills: copy, CRO, SEO, ads, analytics | Marketing |

Install: `npx skills add <owner>/<repo>` or clone into `~/.claude/skills/`.

## super-orchestrator: origin and analysis

### Source
Inspired by DeepSeek's `dsh-superagent-prototype` (MIT, published 31 Oct 2026): an event-driven agent scoped to one GitHub repo that answers issues and mentions with comments and PRs.
- Repo: https://github.com/deepseek-ai/dsh-superagent-prototype
- Overview: https://hermes-ai.net/news/deepseek-releases-a-silent-coding-agent-that-works-without-bothering-you/

### Why not run the prototype itself
- Labelled research prototype by DeepSeek; early field reports list real defects (Node 24 status check, unchecked notification delivery, version gates without guidance). See discussions #11700, #11706 in deepseek-ai/deepseek-harness.
- Needs Linux + tmux + Node + gh, a dedicated GitHub agent account and a paid DeepSeek API key.

### What was kept (5 rules)
1. One git worktree per task, so branches never collide.
2. Deterministic gate before any agent spawn.
3. Orchestrator plans and merges, never edits code or waits on workers.
4. Structured task contract instead of chat, to protect context.
5. Max 3 parallel workers.

### Expected gain (unbenchmarked)
- Large tasks (5+ files): cleaner orchestrator context, no file collisions.
- Small/medium tasks: net loss. Each subagent starts cold, roughly 2-4x tokens. The gate keeps these inline.
- Adds no new capability over Claude Code (worktrees + subagents exist); adds discipline.

### How to test
Run the same 5+ file task twice, inline vs skill. Compare tokens and number of post-fixes. Drop the skill if it doesn't win.
