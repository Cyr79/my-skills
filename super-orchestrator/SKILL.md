---
name: super-orchestrator
description: Use for long multi-file coding tasks (>3 files or >1h). Orchestrator plans + delegates; workers run in isolated git worktrees. Skip for small edits.
---

# Super-orchestrator

## Gate (before any agent spawn)
Spawn workers ONLY if all true, else do task inline:
- task touches >3 files OR has >2 independent parts
- repo is git, clean tree (`git status --porcelain` empty)
- acceptance check exists (test/build/lint cmd)

## Roles
- Orchestrator: plan, write contracts, review diffs, merge. Never edits code, never runs long shells, never waits on a worker (background agents).
- Worker: 1 contract, 1 worktree (`isolation: worktree`), returns diff + check result only.

## Contract (one per worker, file `tasks/<id>.md`)
```
id: T1
goal: <1 line>
files: <allowed paths only>
not: <out of scope>
done_when: <exact cmd that must pass>
return: diff summary <=10 lines + cmd output tail
```

## Split (3 axes, pick one)
- by layer (api / ui / db)
- by file ownership (no 2 workers same file)
- by stage (research -> build -> test), sequential

## Limits
- max 3 parallel workers
- worker fails twice -> orchestrator takes it inline or re-splits
- context: orchestrator keeps only contracts + summaries, never full files

## Merge
1. run done_when in worker branch
2. merge one at a time, rerun full tests after each
3. conflict -> new contract, never manual blind merge
