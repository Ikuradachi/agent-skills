# Ecosystem Detection Reference

Signal files, what to extract from each, and grouping conventions for the dependency inventory in Phase 2 of a project x-ray.

## Detection table

| Signal | What to extract |
|---|---|
| `package.json` / `pnpm-lock.yaml` / `yarn.lock` / `package-lock.json` | Runtime, framework, key deps grouped by role (framework, data, HTTP, auth, test, dev tooling). Node version from `.nvmrc` / `engines`. |
| `pyproject.toml` / `requirements.txt` / `Pipfile` / `setup.py` | Python version, framework (Django/Flask/FastAPI/…), key libraries. |
| `Cargo.toml` / `Cargo.lock` | Rust edition, key crates, workspace members. |
| `go.mod` / `go.sum` | Go version, key modules. |
| `*.csproj` / `*.sln` / `Directory.Build.props` | .NET version, target framework, NuGet packages. |
| `build.gradle` / `pom.xml` | Java/Kotlin version, Spring Boot version, key dependencies. |
| `Gemfile` / `Gemfile.lock` | Ruby version, Rails version, key gems. |
| `composer.json` | PHP version, Laravel/Symfony version, key packages. |
| `mix.exs` | Elixir/Erlang version, Phoenix version, key deps. |
| `pubspec.yaml` | Dart/Flutter version, key packages. |
| `Package.swift` / `*.xcodeproj` | Swift version, platform targets, SPM dependencies. |

## Build tooling detection

Look for these and name the ones present: webpack, vite, esbuild, turbopack, tsc, rollup, parcel, cargo, make, cmake, bazel, gradle, maven, msbuild, mix, go build.

## Test runner detection

jest, vitest, mocha, pytest, unittest, go test, cargo test, xunit, junit, rspec, minitest, ExUnit, flutter_test.

## Linter / formatter detection

eslint, prettier, biome, ruff, black, isort, clippy, rustfmt, gofmt, golangci-lint, checkstyle, spotless, rubocop, credo, dart analyze.

## Grouping convention

Group dependencies by **role**, not alphabetically. A reader should see at a glance:

- **Framework** — the core framework(s) driving the app.
- **Data** — ORMs, query builders, database drivers, caching clients.
- **HTTP / API** — routers, API clients, GraphQL, gRPC.
- **Auth** — authentication and authorization libraries.
- **Test** — test runners, assertion libs, mocking, fixtures.
- **Dev tooling** — bundlers, linters, formatters, type checkers, hot reload.
- **Infra / deploy** — Docker, Terraform, Pulumi, CDK, Helm, serverless frameworks.

Omit groups that have zero entries. If a dependency spans roles, place it in the role that matters most to the project's architecture.
