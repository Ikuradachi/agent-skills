---
name: project-x-ray
description: >
  Deep-scan a project's structure, dependencies, architecture, conventions, and domain,
  then write the findings into a structured PROJECT_ANALYSIS.md file that any human or agent
  can use for fast orientation. Use when the user says "analyze this project", "x-ray the codebase",
  "write a project overview", "onboard me", "what does this project do", "map this repo",
  or when a new agent session needs to understand an unfamiliar codebase before doing real work.
---

# Project X-Ray

Scan a codebase end-to-end and produce a single markdown file — `PROJECT_ANALYSIS.md` — that answers the questions a newcomer (human or agent) would otherwise spend an hour piecing together: what does this project do, how is it built, where does everything live, and what conventions does the team follow?

The skill exists because agents dropped into an unfamiliar repo waste context window rediscovering the same facts — directory layout, framework version, test runner, naming conventions — on every session. A one-time x-ray run captures that context durably so subsequent sessions can load it instead of re-scanning.

## When to use this skill

- The user asks to analyze, scan, or map a project.
- The user asks "what does this project do?" or wants an onboarding overview.
- An agent is starting work in a repo it has never seen and needs fast orientation.
- The user wants a written snapshot of the project state before a large refactor or handoff.

## Budget and depth

A scan that exhausts the context window defeats the purpose. Constraints:

- **Tree depth** — expand key directories to at most 3 levels. Below that, summarize ("47 component files, grouped by feature"). Root-level files always listed.
- **Output cap** — `PROJECT_ANALYSIS.md` should land under ~2,000 lines. If the project is genuinely enormous (large monorepo, 50+ packages), write the top-level analysis and list the packages with one-line descriptions; offer per-package deep scans as follow-ups, not inline.
- **File reads** — read manifests, configs, entry points, and a sampling of representative source files. Do not read every file in the repo. Prioritize files that reveal architecture (routers, DI containers, main entry, schema definitions) over leaf implementations.
- **Prioritize signal** — if something is boring and obvious (e.g., a standard Create React App with no customization), say so in one line instead of enumerating every default file.

## Phase 1: Structure scan

Walk the project directory tree, respecting `.gitignore` and skipping generated/vendored directories (`node_modules`, `dist`, `build`, `.git`, `__pycache__`, `target`, `vendor`, `.next`). Produce:

1. **Top-level tree** — every root-level file and directory with a one-line purpose annotation.
2. **Depth-limited expansion** — expand key directories (src, lib, app, packages, cmd, internal) to 2-3 levels to show the organizational shape without flooding the output.
3. **Entry points** — identify main entry files: `main.go`, `index.ts`, `app.py`, `Program.cs`, `Main.java`, `Cargo.toml [[bin]]`, `package.json#main`, etc.
4. **Monorepo detection** — check for `workspaces` in package.json, `pnpm-workspace.yaml`, `Cargo.toml [workspace]`, `go.work`, `lerna.json`, `nx.json`. If detected, list the packages/apps with one-line descriptions.

## Phase 2: Dependency & toolchain inventory

Detect the project's ecosystem from manifest and config files. The full detection table — which files to look for, what to extract, and how to group dependencies by role — is in **[references/ecosystems.md](references/ecosystems.md)**.

The short version: find the manifests, extract versions and key dependencies, group by role (framework, data, HTTP, auth, test, dev tooling), and detect the build tooling, test runner, and linter/formatter. A reader should see "these are the data-access libs, these are the test libs" at a glance.

## Phase 3: Architecture mapping

Identify the architectural shape by examining directory layout, import patterns, and framework conventions:

1. **Pattern classification** — determine the dominant pattern: MVC, layered (handler → service → repository), hexagonal/ports-and-adapters, microservices (multiple deploy units), CLI tool, library/SDK, monolith, serverless functions, plugin system, pipeline/ETL. State which one and cite the directory structure or code patterns that reveal it.

   **When the pattern is unclear:** say so. Many real codebases are hybrids, have evolved through multiple architectures, or simply lack a coherent pattern. Do not force-classify — "mixed: started as MVC, service layer added later, some handlers bypass it" is more useful than a clean label that misleads. Cite the evidence on both sides: which parts follow a pattern, which parts don't, and where the seams are.

2. **Module map** — list the major modules/packages/namespaces and their single-sentence responsibility.
3. **Data flow** — describe how a request/command flows through the system. Example: "HTTP request → router → handler → service → repository → Postgres." For projects with multiple entry points (CLI + API, worker + web), trace each separately.
4. **Boundaries** — note any clear boundary enforcement: separate packages, interface layers, dependency injection, API versioning. Also note where boundaries are *absent* — a service layer that directly imports UI components, a handler that writes SQL inline — since missing boundaries are often more useful to flag than present ones.
5. **Diagram** — when the project has ≤15 major modules and the relationships are non-trivial, include a mermaid graph showing the dependency/data-flow direction. Skip if the project is flat enough that a diagram adds nothing (e.g., a single-file CLI tool), or large enough that the diagram becomes unreadable (in which case, diagram the top-level package relationships only). The diagram should reveal structure a reader couldn't get from the module list alone — if it's just the module list with arrows, it's not earning its space.

## Phase 4: Convention extraction

Scan configuration files and code samples to extract the team's conventions:

- **Code style** — indentation (tabs/spaces/size), naming convention (camelCase, snake_case, PascalCase for types), file naming pattern (kebab-case, PascalCase, snake_case).
- **Commit conventions** — check for `.commitlintrc` or other configuration files for conventional commits (do not scan git logs or history).
- **Branching model** — check for branch protection config or PR templates (do not scan git branches).
- **CI/CD** — detect `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `.circleci/`, `bitbucket-pipelines.yml`, `azure-pipelines.yml`. Summarize what the pipeline does (lint, test, build, deploy) without dumping full YAML.
- **Documentation** — note existing docs: README, CONTRIBUTING, CHANGELOG, ADRs (`docs/adr/`), API docs, OpenAPI/Swagger specs.
- **Agent instruction files** — flag any files meant for AI agents: `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.github/copilot-instructions.md`, `GEMINI.md`. Note their existence and one-line summary of what they instruct.

## Phase 5: Domain summary

Write 2-4 paragraphs that describe what the project **does** in plain language:

- What problem does it solve and for whom?
- What are the core domain concepts / entities?
- What are the primary user-facing features or API surfaces?
- How is the project deployed or consumed (npm package, Docker container, CLI binary, web app, mobile app, library)?

Ground every claim in evidence: README content, code comments, route definitions, exported functions, CLI help text, test descriptions. Do not speculate beyond what the code shows — put genuine unknowns in the Open Questions section.

## Phase 6: Consumption guidance

The analysis file is useless if nobody loads it. End `PROJECT_ANALYSIS.md` with a short section telling readers how to use it:

- **Agents** — load `PROJECT_ANALYSIS.md` at session start instead of re-scanning. If the file exists and is recent (< 7 days, no major structural changes since), skip the scan and work from it.
- **Humans** — read Overview and Architecture first; the rest is reference. Ctrl-F for specific tech or dependency questions.
- **Staleness** — the analysis is a point-in-time snapshot. Re-run x-ray after major refactors, dependency upgrades, or architectural changes. The `Generated by` date in the header is the freshness signal.

## Output format

Write the analysis to `PROJECT_ANALYSIS.md` in the repository root (or another path if the user specifies one). Use this structure:

```markdown
# Project Analysis: {project name}

> Generated by project-x-ray · {YYYY-MM-DD}

## Overview

{Phase 5 domain summary — 2-4 paragraphs}

## Directory Structure

{Phase 1 annotated tree + entry points}

## Tech Stack & Dependencies

{Phase 2 ecosystem, versions, grouped deps, build/test/lint tooling}

## Architecture

{Phase 3 pattern, module map, data flow, optional diagram}

## Conventions & Tooling

{Phase 4 code style, commits, CI/CD, docs, agent files}

## Using This Analysis

{Phase 6 consumption guidance}

## Open Questions

{Things the scan couldn't determine — missing docs, ambiguous patterns,
 decisions that need a human to clarify}
```

If `PROJECT_ANALYSIS.md` already exists, overwrite it — the file is a point-in-time snapshot, not a log. Tell the user what you wrote and where.

## Skill pairing

X-Ray output composes well with other skills:

- **context-canary** — load `PROJECT_ANALYSIS.md` as session context; canary monitors whether the agent's grip on that context degrades.
- **junior-to-senior** — feed the analysis into a plan review so the senior reviewer has codebase grounding without re-scanning.
- **loop-factory** — use the architecture and convention sections to write better task specs.

## Boundaries

- **Report, don't judge.** The x-ray describes what is; it does not rate code quality, suggest refactors, or prescribe changes. That's a different skill.
- **No secrets.** Note the existence of `.env`, `.env.local`, secrets directories, vault configs — but never read or reproduce their contents.
- **Respect .gitignore.** If a directory is gitignored, skip it. Generated and vendored code is noise, not signal.
- **Open questions are honest.** If the scan can't determine something (e.g., deploy target, database choice when there's no config), say so in the Open Questions section. Don't invent answers.
- **Read-only.** The scan creates exactly one file (`PROJECT_ANALYSIS.md`). It does not modify source code, rewrite the README, create additional analysis files, or restructure the project.
- **Don't re-scan unnecessarily.** If a recent `PROJECT_ANALYSIS.md` exists (< 7 days, no major structural changes), load it and work from it instead of re-running the full scan. Offer to re-scan if the user asks or if the file is clearly stale.
- **Phases run in order.** Structure → Dependencies → Architecture → Conventions → Domain → Consumption. Earlier phases feed later ones. Don't skip phases or jump ahead — a domain summary without a structure scan is guesswork.
