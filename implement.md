---
description: "Decide whether to create beads or implement directly from a plan/task file"
---

# Implement from plan

Evaluate the plan or task file, then choose the right execution strategy.

## Decision criteria

Create beads issues when **any** of these are true:
- 4+ independent tasks that benefit from parallel agents
- Work will span multiple sessions or need handoff
- Tasks have real dependency ordering (not just "do A before B in the same file")
- Multiple people/agents will claim separate tasks

Implement directly when **all** of these are true:
- 3 or fewer tasks, or tasks are tightly coupled
- Single session, single agent
- Tasks touch overlapping files (beads + worktrees = merge conflicts)
- Overhead of issue creation exceeds tracking value

## Branching

<critical>
NEVER implement on main, master, or develop. Always create a feature branch first.
</critical>

**Use a worktree** when:
- Parallel agents need to work on independent tasks simultaneously
- You need to keep the main workspace clean for reference/testing
- Multiple branches need to exist side by side (e.g., comparing approaches)

**Use a regular branch** when:
- Single agent, sequential work
- Tasks touch overlapping files (worktree merges get messy)
- Small changes where worktree setup overhead isn't worth it

```bash
# Regular branch (default)
git checkout -b feat/<short-description>

# Worktree (parallel agents only)
# Use the /using-git-worktrees skill
```

## Execution

**Direct implementation:**
1. Create feature branch
2. Read the plan file
3. Work through tasks in dependency order
4. Commit when done

**Beads-tracked implementation:**
1. Create feature branch
2. Read the plan file
3. Create beads issues (`bd create`) — parallelize creation with subagents if 4+
4. Set up dependencies with `bd dep add` where needed
5. Run `/work-on-beads` to execute

<critical>
Default to direct implementation. Only create beads when the criteria clearly justify it. Tracking overhead on small work is waste.
</critical>
