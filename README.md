# pi-superpowers

A [pi](https://github.com/badlogic/pi) package that bundles **pi-subagents** (orchestration engine) with opinionated development methodology skills. One install gives you subagent delegation, structured workflows, and discipline guardrails.

## Install

```bash
pi install git:github.com/swissonid/pi-superpowers
```

## What You Get

### Orchestration (via pi-subagents)

The full subagent toolkit — delegate work to specialized agents:

- **Agents:** worker, reviewer, scout, planner, oracle, researcher, context-builder, delegate
- **Modes:** single, parallel, chain, async, forked-context, worktree-isolated
- **Coordination:** intercom bridge, control signals, soft-interrupt, resume

### Skills

| Skill | When it activates |
|---|---|
| **subagent-driven-development** | Executing a plan — dispatches one implementer per task with two-stage review |
| **systematic-debugging** | Any bug, test failure, or unexpected behavior |
| **test-driven-development** | Implementing any feature or fix |
| **verification-before-completion** | Before claiming work is done |
| **brainstorming** | New features, creative work, before implementation |
| **writing-plans** | Multi-step tasks that need a plan document |
| **receiving-code-review** | Evaluating feedback from reviewers |
| **finishing-a-development-branch** | Work is done, ready to merge/PR/discard |

### Prompt Shortcuts

| Command | What it does |
|---|---|
| `/sdd <plan-file>` | Execute plan with Subagent-Driven Development |
| `/brainstorm` | Start collaborative design exploration |
| `/write-plan` | Create implementation plan from spec/requirements |
| `/parallel-review` | Fresh-context adversarial review from multiple angles |
| `/review-loop` | Iterative worker → reviewer → fix cycles until clean |
| `/parallel-research` | Combine web research with local code context |
| `/parallel-context-build` | Build structured handoff context for planning |
| `/parallel-handoff-plan` | Research + context → implementation-ready brief |
| `/gather-context-and-clarify` | Scout/research, then ask clarifying questions |
| `/parallel-cleanup` | Adversarial deslop + verbosity review passes |

## Subagent-Driven Development

The headline workflow. Given a plan with independent tasks:

```
For each task:
  Implementer (worker) → Spec Compliance Review → Code Quality Review → Next
```

Key features:
- **Fresh context per task** — no pollution between tasks
- **Two-stage review** — first checks spec match, then code quality
- **Status protocol** — implementer reports DONE / DONE_WITH_CONCERNS / NEEDS_CONTEXT / BLOCKED
- **Model routing** — cheap models for mechanical work, capable models for architecture
- **Continuous execution** — no stopping between tasks unless blocked

```bash
/sdd docs/plans/2025-05-19-auth-migration.md
```

## The Full Development Flow

```
/brainstorm          → explore idea, write spec
/write-plan          → create bite-sized implementation plan
/sdd <plan>          → execute with per-task review gates
                     → finishing-a-development-branch (merge/PR/keep)
```

Each step uses the appropriate skill automatically. Along the way:
- `systematic-debugging` kicks in when things break
- `test-driven-development` guides implementation
- `verification-before-completion` prevents premature "done" claims
- `receiving-code-review` handles reviewer feedback properly

## Updating

pi-subagents is bundled as a dependency. To get upstream updates:

```bash
cd /path/to/pi-superpowers
npm update pi-subagents
git add -A && git commit -m "chore: update pi-subagents"
git push
```

Your skills and prompts are yours — edit them freely without worrying about upstream conflicts.

## Structure

```
pi-superpowers/
├── package.json                     # pi package manifest + pi-subagents dependency
├── skills/
│   ├── subagent-driven-development/ # SDD workflow + prompt templates
│   ├── systematic-debugging/        # 4-phase root cause methodology
│   ├── test-driven-development/     # Red-green-refactor discipline
│   ├── verification-before-completion/
│   ├── brainstorming/               # Idea → spec collaborative flow
│   ├── writing-plans/               # Spec → bite-sized plan
│   ├── receiving-code-review/       # How to evaluate feedback
│   └── finishing-a-development-branch/
├── prompts/
│   ├── sdd.md                       # /sdd command
│   ├── brainstorm.md                # /brainstorm command
│   └── write-plan.md                # /write-plan command
└── node_modules/pi-subagents/       # Bundled orchestration engine
```

## Credits

- Methodology skills adapted from [superpowers](https://github.com/superpowers-ai/superpowers)
- Orchestration powered by [pi-subagents](https://github.com/nicobailon/pi-subagents)

## License

MIT
