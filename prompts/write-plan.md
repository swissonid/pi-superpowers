---
name: write-plan
description: Write a detailed implementation plan with bite-sized TDD tasks, exact file paths, and complete code
---

# Write Plan

Use the `writing-plans` skill to create an implementation plan.

Expects either:
- A spec/design doc path as argument
- Requirements described inline

Produces a plan at `docs/plans/YYYY-MM-DD-<feature-name>.md` with:
- Bite-sized tasks (2-5 min steps)
- TDD: failing test → verify fail → implement → verify pass → commit
- Complete code in every step (no placeholders)
- Exact file paths and commands
