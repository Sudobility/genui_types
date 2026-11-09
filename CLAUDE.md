# CLAUDE.md

Guidance for working in this repo. Keep it accurate; see README.md for the fuller tour.

## What this is

`@sudobility/genui_types` — a **types-only** npm package of shared TypeScript
definitions for GenUI (server-driven UI). Everything lives in one file:
`src/index.ts`. No runtime logic beyond `as const` enum objects.

## Stack

- TypeScript (`~5.9`), ESM-only (`"type": "module"`).
- **Bun** is the package manager/runner (`bun.lock` committed); scripts invoke
  `bun run ...`. ESLint 9 (flat config), Prettier, Vitest 4.

## Commands (verified against package.json)

- `bun install` — deps. Needs GitHub Packages auth (see gotcha below).
- `bun run verify` — the full gate: typecheck + lint + test:unit + build. Run this
  before declaring work done.
- `bun run typecheck` — `tsc --noEmit` (uses `tsconfig.json`, `noEmit: true`).
- `bun run build` — `tsc -p tsconfig.esm.json` → `dist/`. Build config is a
  separate file; `tsconfig.json` itself never emits.
- `bun run lint` / `lint:fix` — `eslint src`.
- `bun run test:unit` (one-shot) / `test:watch` — Vitest; tests are `src/*.test.ts`.
- `bun run format` / `format:check` — Prettier over `src`.

## Architecture / conventions

- **Single source file.** Add new types/enums to `src/index.ts` and they are
  exported from the package root (`exports["."]`). No barrel/subpath structure.
- **Enum pattern:** declare a `const X = {...} as const` object AND
  `type X = (typeof X)[keyof typeof X]` with the same name, so the identifier works
  as both value and type. Follow this for any new enum.
- Interfaces use `?: T | null` liberally — fields are both optional and nullable.
- Prettier: single quotes, semicolons, 80-col, 2-space, `trailingComma: es5`.
  `prettier/prettier` is an ESLint error, so lint fails on unformatted code.
- Tests mirror exports with simple value assertions; keep them in sync when adding
  enums.

## Gotchas

- **Install needs auth.** `.npmrc` routes `@sudobility:registry` to
  `https://npm.pkg.github.com`. Without a token that has `read:packages`,
  `bun install` fails. Auth token lives in `~/.npmrc`, not committed.
- **Enum string values aren't always the key.** Some `ThemeColor` values differ
  from their key (e.g. `TABLE_BACKGROUND: 'BACKGROUND_TABLE'`). Don't assume
  key === value. `ScreenLayout`/`ViewLayout` values are lowercase.
- Only `dist/**` is published (`files`). `prepublishOnly` runs `clean` + `verify`.
- CI uses the external reusable workflow `johnqh/workflows` with
  `publish-target: github`; this repo's CI file is a thin wrapper.
