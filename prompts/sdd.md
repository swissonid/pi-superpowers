---
name: sdd
description: Execute a plan using Subagent-Driven Development — one implementer per task with two-stage review (spec compliance then code quality)
---

# Subagent-Driven Development

Execute the provided plan using the `subagent-driven-development` skill.

## Instructions

1. Read the `subagent-driven-development` skill
2. Read the plan file provided as argument
3. Extract all tasks with full text and context
4. Execute the SDD workflow: per-task implementer → spec review → quality review → next task
5. After all tasks complete, run a final review pass
6. Use the `finishing-a-development-branch` skill to complete work

## Key Rules

- Fresh subagent per task (never inherit session context)
- Two-stage review: spec compliance FIRST, then code quality
- Do not proceed past a failing review
- Do not pause between tasks to ask the user — execute continuously
- Select model tier by task complexity (cheap for mechanical, capable for architecture)
