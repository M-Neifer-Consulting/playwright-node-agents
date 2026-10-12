# Copilot instructions

## Project

This is a small TypeScript example project demonstrating Playwright test agents. It is not an application or a reusable library: its only executable code is a sample browser-test suite. The repository has 11 tracked files before this instruction file. Tests run on Node.js (`.node-version`: 24.21.0) with npm and `@playwright/test` 1.64.0; this environment has npm 11.19.0, and CI selects the latest Node.js LTS.

## Layout and conventions

- `tests/` contains Playwright tests; `tests/example.spec.ts` is the sample test and uses `https://playwright.dev/`.
- `playwright.config.ts` sets `tests/` as the test directory and runs each test in Chromium, Firefox, and WebKit. CI enables retries, one worker, and `forbidOnly`.
- `package.json` and the committed `package-lock.json` define the npm dependencies. Keep the lockfile in sync with dependency changes.
- `.github/workflows/playwright.yml` is the CI check. `.github/dependabot.yml` configures weekly npm and GitHub Actions updates.
- Follow the existing TypeScript style (two-space indentation in TS files). `.editorconfig` sets general editor defaults.

## Install and validate

From the repository root:

1. Always run `npm ci` after checkout or dependency/lockfile changes. This installs the exact lockfile versions; it succeeds with the pinned Node/npm toolchain.
2. Ensure Playwright browser binaries and their operating-system libraries are available. In CI, the workflow runs `npx playwright install --with-deps`. Locally, `npx playwright install` installs browser binaries; OS-library installation may require administrator access. In restricted environments, `--with-deps` can fail because it invokes `sudo` and may require an interactive terminal; use preinstalled OS dependencies or have them provisioned by the environment rather than treating this as a test failure.
3. Run `npx playwright test` to execute the suite (six tests across Chromium, Firefox, and WebKit at present). This requires browser binaries and network access to `playwright.dev`. For CI-like behavior, run `CI=true npx playwright test`; it uses one worker and enables CI retries/`forbidOnly`.

The workflow order is `npm ci` → `npx playwright install --with-deps` → `npx playwright test`; it has a 60-minute job timeout and uploads `playwright-report/`. Local verification in this environment: `npm ci` and `npx playwright install` succeeded; both `npx playwright test` and `CI=true npx playwright test` passed all six tests. `npx playwright install --with-deps` failed only at the privileged OS-dependency step (`sudo: A terminal is required to authenticate`). The browser install without `--with-deps` succeeded; it was already satisfied by the available browser cache.

There are no npm scripts, build, type-check, or lint commands configured, and no app/server to launch. Do not invent `npm run build`, `npm test`, or lint steps; use the Playwright CLI above. Playwright transpiles the TypeScript tests, but this repository does not configure a separate TypeScript type-check.

## Change guidance

Keep changes focused on the example and its tests/configuration. Add or update browser coverage for behavior changes, and avoid relying on local browser state or network-independent assumptions: the current tests explicitly navigate to the public Playwright site. Use these instructions as the source of truth; search the repository only if a needed detail is missing here or an instruction appears inaccurate.
