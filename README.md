# Julius Skills

Five personal agent skills for now: Caveman base, Interface Kit, Grill Me, Loop Factory, and Junior to Senior.

This repo is shaped by five things:

- **Caveman** - 70k-star token compression without technical loss. Small mouth, big brain.
- **Interface Kit** - accessible, performant interfaces with strong aesthetic direction, not generic AI slop.
- **Grill Me** - calibrated pressure before hard critique, so challenge matches user knowledge and comfort.
- **Loop Factory** - spec-driven agent loop where tasks move through inbox → active → archive with a real review gate.
- **Junior to Senior** - adversarial senior review that treats agent output as junior work and upgrades it with codebase + web research.

Point is control. Agents should be terse when talking, precise when building interfaces, calibrated when challenging plans, and disciplined when running build loops.

## Quickstart

Install with `skills.sh`:

```bash
npx skills@latest add JuliusBrussee/skills
```

Pick the skills you want for Claude Code, Codex, Gemini, Cursor, Windsurf, Cline, Copilot, or any agent supported by the installer.

For the canonical Caveman installer, use:

```bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.sh | bash
```

## Why These Skills Exist

Most agent output fails in three boring ways: too many words, UI that looks like every other generated demo, or critique that starts too hard before understanding user context.

This repo keeps three fixes close:

1. **Speak less, say more.** Caveman cuts output tokens while preserving exact commands, code, errors, and technical meaning.
2. **Build interfaces with taste.** Interface Kit starts from accessibility, performance, typography, spatial rhythm, color roles, and concrete interaction states.
3. **Challenge at the right altitude.** Grill Me assesses knowledge and desired pressure first, then asks one question at a time.

## Skills

### `caveman`

Ultra-compressed communication mode. Cuts token usage by dropping filler, hedging, pleasantries, and excess grammar while keeping technical accuracy intact.

Use when you want:

- shorter agent replies
- less token waste
- exact code, command, error, and API preservation
- persistent terse style until user exits with "normal mode"

### `interface-kit`

Implementation guide for high-quality UI. Synthesizes accessibility, performance, typography, layout, color systems, motion, interaction states, and component craft.

Use when building:

- frontend components
- landing pages
- dashboards
- design systems
- polished app flows
- accessibility fixes
- UI review passes

### `grill-me`

Calibrated interview skill for stress-testing plans, designs, and decisions. It first asks how much the user knows and how hard they want the pressure, then ramps from clarifying questions to failure-mode critique.

Use when you want:

- plan critique without getting overwhelmed
- one question at a time
- recommended answers with each question
- pressure matched to beginner, working, or expert knowledge
- softer or harder grilling on command

### `loop-factory`

Spec-driven agent loop. Coding tasks live as markdown specs that move through `inbox → active → archive`, get implemented by Claude Code or Codex, and must pass a review gate before they count as done. State is just which folder a spec is in. The governing rule: automate implementation and verification, not product decisions.

Use when you want:

- repeatable, reviewable agent work instead of one-off prompting
- visible task state (inbox / active / archive) with no dashboard
- generated implementation, review, and backprop prompts for either agent
- a hard review gate before anything is marked done
- to install or scaffold the `loop-factory` CLI into a project

Pairs with the [Loop-Factory](https://github.com/JuliusBrussee/Loop-Factory) repo, which ships the CLI and native Claude/Codex adapters.

### `junior-to-senior`

Adversarial review skill for agent-generated plans. Treats the current output as the work of a junior, then constructs a senior reviewer grounded in codebase research and web research of current best practices. Diagnoses altitude failures — plans that are foggy on the hard parts or tunneled into details with no product vision — and rewrites them into a scoped, state-of-the-art version with evidence behind every finding.

Use when you want:

- a staff-engineer-grade review of a plan before committing to it
- plans that commit on interfaces, versions, and failure modes instead of hand-waving
- best practices refreshed past the model's training cutoff via live web research
- a clear delta between the original plan and the upgraded one
- product decisions surfaced as open questions instead of silently invented

## Interface Kit Standard

If a repo has `DESIGN.md`, it wins. Otherwise UI work should still have a point of view:

- Accessibility first: contrast, keyboard navigation, focus states, semantics.
- Performance before decoration: stable layout, lazy assets, transform-only motion.
- Typography matters: readable type scale, sane line length, tabular numbers for data.
- Layout uses stable spatial rules: 4/8px spacing, responsive constraints, no overlap.
- Components have complete states: hover, focus, active, disabled, loading, empty, error.
- Avoid generic defaults: no faceless hero, no purple-blue gradient template, no stock-looking polish.

## Caveman Ecosystem

This repo sits next to broader Julius agent stack:

| Repo | What |
|---|---|
| [caveman](https://github.com/JuliusBrussee/caveman) | Output compression for agents. |
| [caveman-code](https://github.com/JuliusBrussee/caveman-code) | Terminal coding agent built around token efficiency. |
| [cavemem](https://github.com/JuliusBrussee/cavemem) | Cross-agent memory. |
| [cavekit](https://github.com/JuliusBrussee/cavekit) | Spec-driven build loop. |
| [cavegemma](https://github.com/JuliusBrussee/finetune-caveman) | Fine-tuned model experiments for terse agent output. |

## Skill Reference

Run:

```bash
node scripts/list-skills.mjs
```

Verify frontmatter:

```bash
node scripts/verify-skills.mjs
```

## Contributing

Skills should be:

- Small enough to read quickly.
- Triggered by clear user intent.
- Progressive: load only the extra files needed.
- Specific about workflow and validation.
- Free of repo-specific assumptions unless the skill says so.

## License

MIT.
