# Repository Guidelines

## Project Structure & Module Organization

`apps/` contains runnable targets: `web-*` Vite frontends for different UI stacks and `backend-mock` for the Nitro-based mock API. `packages/` holds shared workspace libraries such as `utils`, `styles`, `stores`, `types`, `effects`, and `@core/*`. `playground/` is the sandbox app used for integration work and Playwright E2E tests. `docs/` contains the VitePress site, `internal/` contains shared lint/build config packages, and `scripts/` contains workspace tooling.

Place feature code under the owning app or package, and re-export public APIs from each package `src/index.ts`.

## Build, Test, and Development Commands

- `pnpm install`: install workspace dependencies; Node `^20.19.0 || ^22.18.0 || ^24.0.0` and `pnpm >=10` are required.
- `pnpm dev`: start the turbo dev pipeline for the workspace.
- `pnpm dev:antd` / `pnpm dev:naive` / `pnpm dev:play`: run one target app locally.
- `pnpm build`: build the full monorepo with Turbo.
- `pnpm check`: run dependency, circular import, type, and spelling checks.
- `pnpm lint` or `pnpm format`: run the shared `vsh` lint/format workflow.
- `pnpm test:unit`: run Vitest with `happy-dom`.
- `pnpm test:e2e`: run Playwright tests from `playground/__tests__/e2e`.

## Coding Style & Naming Conventions

Use 2-space indentation, LF line endings, UTF-8, single quotes, and a soft 100-character line limit as defined in `.editorconfig`. TypeScript and Vue are the default stack. Follow existing naming patterns: package folders use kebab-case, Vue SFCs often use kebab-case filenames, and tests use `*.test.ts` or `*.spec.ts`. Formatting and linting are enforced through `oxfmt`, `oxlint`, `eslint`, and `stylelint` via `lefthook`.

## Testing Guidelines

Keep unit tests close to source files in `__tests__` directories, for example `packages/@core/base/shared/src/utils/__tests__/date.test.ts`. Put browser flows in `playground/__tests__/e2e/*.spec.ts`. Run targeted checks before opening a PR, especially `pnpm test:unit`, `pnpm test:e2e`, and `pnpm check` when shared packages or configs change.

## Commit & Pull Request Guidelines

Recent history is minimal, but it already uses a conventional prefix (`chore:`). Follow Conventional Commits such as `feat(auth): add token refresh` or `fix(table): guard empty state`. The repo includes `commitlint` and a `pnpm commit` helper (`czg`).

PRs should describe the user-facing change, list affected apps/packages, link the issue when available, and include screenshots or recordings for UI changes. Add a changeset when the change affects published packages.
