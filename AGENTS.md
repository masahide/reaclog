# Repository Guidelines

## Project Structure & Module Organization

Main backend runtime lives in `src/index.ts`, with domain modules organized under `src/core`, `src/slack`, `src/pipeline`, `src/io`, and `src/runtime`. The browser workspace lives in `apps/browser` (SvelteKit + Vitest). Long-form design notes sit in `docs/spec.md` and should be kept in sync with architecture changes. Operational helpers for CDP proxies and Slack launchers reside under `hack/`. Keep temporary data and generated artefacts outside tracked source directories.

## Build, Test, and Development Commands

Use `pnpm install` to refresh dependencies when package manifests change. `pnpm start` executes `tsx src/index.ts` and expects a Slack desktop session exposing CDP at `CDP_HOST` and `CDP_PORT`. Use `pnpm run dev` for local development (`scripts/dev.ts`) and `pnpm run serve` for runtime serving (`scripts/serve.ts`). Build backend and browser with `pnpm run build:runtime` (or `build:backend` / `build:browser` separately). Quality gates are bundled in `pnpm run qa`: typecheck, lint, format, backend tests, browser sync/check/typecheck, and browser tests. Use `pnpm check` for a quick gate (format, typecheck, test). Spot fixes are available via `pnpm run lint:fix` and `pnpm run format:write`.

## Coding Style & Naming Conventions

Code is TypeScript-first with ESM modules. Prefer explicit named exports over default exports unless framework conventions require otherwise (for example in SvelteKit-specific entry points). Prettier enforces two-space indentation and double-quoted strings, so avoid manual formatting and let the formatter run. Follow camelCase for variables and functions, PascalCase for types and classes, and uppercase snake case for constants like `CDP_PORT`. ESLint is configured with `@typescript-eslint`; treat warnings as issues to clean up, not to suppress.

## Testing Guidelines

Backend tests live under `tests/` as `*.test.ts` and run with Node's built-in test runner via `node --import tsx --test`. Use `pnpm test` for all backend tests, or `pnpm run test:slack` for Slack-focused suites in `tests/slack/`. Browser tests run inside `apps/browser` via the workspace `test` script and are included in `pnpm run qa`. Keep tests close to the behavior they validate, and record manual verification steps in PRs for CDP/Slack flows (workspace, channel, and timestamp context).

## Commit & Pull Request Guidelines

Recent history favors Conventional Commits (`feat:`, `fix:`, `chore:`) with imperative summaries. Keep commit subjects under 72 characters and group related changes into one commit. Avoid mixing behavior changes with broad formatting-only edits. Pull requests must describe intent, include verification commands (normally `pnpm run qa`), and link tracking issues. Attach screenshots or logs when Slack interaction behavior changes. Request review only after confirming `git status` is clean after quality checks.

## CDP & Slack Environment Notes

Slack automation depends on a reachable Chrome DevTools endpoint; confirm availability with `chrome-remote-interface` before running the collector. The scripts in `hack/` help bridge WSL and Windows Slack sessions. Update those scripts when port numbers or launch paths change, and document required environment variables in the scripts or `hack/README.md`. Protect workspace tokens by using `.env.local` or shell exports; never commit credentials or raw Slack payloads to the repository.
