---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks. Dispatches a fresh subagent per task with two-stage review (spec compliance then code quality) after each. Requires pi-subagents.
---

# Subagent-Driven Development

Execute a plan by dispatching a fresh subagent per task, with two-stage review after each: spec compliance review first, then code quality review.

**Why subagents:** You delegate tasks to specialized agents with isolated context. By precisely crafting their instructions and context, you ensure they stay focused and succeed at their task. They should never inherit your session's context or history — you construct exactly what they need. This also preserves your own context for coordination work.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration

**Continuous execution:** Do not pause to check in with your human partner between tasks. Execute all tasks from the plan without stopping. The only reasons to stop are: BLOCKED status you cannot resolve, ambiguity that genuinely prevents progress, or all tasks complete.

## When to Use

Use when you have:
- An implementation plan with independent tasks
- Tasks that can be executed by a focused subagent in isolation
- Need for quality gates between tasks

**vs. plain review-loop:** Review-loop reviews a whole diff iteratively. SDD reviews per-task against the task spec, catching drift early before it compounds.

**vs. parallel workers:** SDD is sequential (one implementer at a time) to avoid conflicts. Use parallel dispatch only for read-only tasks (research, review, context-building).

## The Process

```
For each task in plan:
  1. Dispatch implementer (worker agent, fresh context)
  2. Handle implementer status (DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED)
  3. If DONE or DONE_WITH_CONCERNS → spec compliance review (fresh reviewer)
  4. If spec passes → code quality review (fresh reviewer)
  5. If either review fails → implementer fixes → re-review
  6. Mark task complete → next task

After all tasks:
  Final review pass across entire implementation
```

## Model Selection

Use the least powerful model that can handle each role to conserve cost and increase speed.

| Task type | Signal | Model tier |
|-----------|--------|-----------|
| Mechanical implementation | 1-2 files, complete spec, isolated function | Fast/cheap |
| Integration work | Multi-file coordination, pattern matching | Standard |
| Architecture & review | Design judgment, broad codebase understanding | Most capable |

## Implementer Status Protocol

Implementer subagents report one of four statuses:

**DONE:** Proceed to spec compliance review.

**DONE_WITH_CONCERNS:** Completed but flagged doubts. Read concerns before proceeding. If about correctness/scope → address before review. If observations → note and proceed.

**NEEDS_CONTEXT:** Missing information. Provide context and re-dispatch.

**BLOCKED:** Cannot complete. Assess:
1. Context problem → provide more context, re-dispatch same model
2. Needs more reasoning → re-dispatch with more capable model
3. Task too large → break into smaller pieces
4. Plan is wrong → escalate to human

**Never** ignore an escalation or force retry without changes.

## Dispatching the Implementer

Use the prompt template at `./implementer-prompt.md`. Key elements:

```typescript
subagent({
  agent: "worker",
  task: `/* Fill implementer-prompt.md template with:
    - Full task text from plan (never make subagent read the plan file)
    - Scene-setting context (where this fits, dependencies)
    - Working directory
    - Any answers to anticipated questions
  */`,
  async: true,
  context: "fresh"  // or omit for default forked context
})
```

## Spec Compliance Review

After implementer reports DONE, dispatch a fresh reviewer using `./spec-reviewer-prompt.md`:

```typescript
subagent({
  agent: "reviewer",
  task: `/* Fill spec-reviewer-prompt.md with:
    - Full task requirements text
    - Implementer's claims about what was built
    - Instruction to verify by reading code, NOT trusting the report
  */`,
  context: "fresh"
})
```

The spec reviewer checks:
- **Missing requirements** — things skipped or not actually implemented
- **Extra work** — over-engineering, features not in spec
- **Misunderstandings** — wrong interpretation of requirements

## Code Quality Review

**Only after spec compliance passes.** Use `./quality-reviewer-prompt.md`:

```typescript
subagent({
  agent: "reviewer",
  task: `/* Fill quality-reviewer-prompt.md with:
    - Task summary
    - BASE_SHA and HEAD_SHA for the diff
    - Plan/requirements reference
  */`,
  context: "fresh"
})
```

## Handling Review Failures

When either reviewer finds issues:
1. Resume the implementer with specific fix instructions
2. Implementer fixes and reports back
3. Re-dispatch the same reviewer
4. Repeat until approved

```typescript
subagent({
  action: "resume",
  id: "implementer-run-id",
  message: "Spec reviewer found issues:\n- Missing: progress reporting\n- Extra: --json flag not in spec\n\nFix these and report back."
})
```

## Example Workflow

```
[Read plan, extract 5 tasks]

Task 1: Hook installation script
├─ Dispatch implementer (cheap model, 1-2 files)
├─ Implementer: DONE
├─ Spec reviewer: ✅ compliant
├─ Quality reviewer: ✅ approved
└─ Mark complete

Task 2: Recovery modes  
├─ Dispatch implementer (standard model, multi-file)
├─ Implementer: DONE
├─ Spec reviewer: ❌ missing progress reporting, extra --json flag
├─ Resume implementer with fixes
├─ Implementer: DONE
├─ Spec reviewer: ✅ compliant
├─ Quality reviewer: Important: magic number
├─ Resume implementer: extract constant
├─ Quality reviewer: ✅ approved
└─ Mark complete

...

[All tasks complete]
Final review pass across entire implementation
Use finishing-a-development-branch skill
```

## Red Flags

**Never:**
- Start implementation on main/master without explicit consent
- Skip reviews (spec compliance OR code quality)
- Proceed with unfixed issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Start code quality review before spec compliance passes
- Move to next task while either review has open issues
- Let implementer self-review replace actual review (both needed)

**If subagent asks questions:** Answer clearly and completely before proceeding.

**If subagent fails:** Dispatch fix with specific instructions. Don't fix manually (context pollution).

## Prompt Templates

- `./implementer-prompt.md` — Dispatch implementer subagent
- `./spec-reviewer-prompt.md` — Dispatch spec compliance reviewer
- `./quality-reviewer-prompt.md` — Dispatch code quality reviewer

## Integration

Works with:
- **pi-subagents** — provides the `subagent()` tool, agents, async, chains
- **writing-plans** — creates the plan this skill executes
- **test-driven-development** — implementers follow TDD
- **verification-before-completion** — verify before claiming done
- **finishing-a-development-branch** — complete work after all tasks
