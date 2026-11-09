# @sudobility/genui_types

Shared TypeScript type definitions for **GenUI** — the server-driven UI model used
across Sudobility apps. It describes a renderable UI tree (`IRenderable`,
`IRenderableView`, `IRenderableScreen`) plus the enumerations that style and lay it
out (`ThemeColor`, `FontWeight`, `ScreenLayout`, `ViewLayout`).

This is a types-only package: it ships a handful of runtime constant objects
(`as const` enums) and the TypeScript interfaces/types that go with them. There is
no other runtime logic.

## What's in it

All exports live in a single module, [`src/index.ts`](src/index.ts):

- **`FontWeight`** — text roles: `TITLE`, `SUBTITLE`, `BODY`, `VALUE`, `FOOTNOTE`.
- **`ThemeColor`** — semantic color tokens (labels, backgrounds, system colors,
  action/nav colors, status colors, etc.).
- **`ScreenLayout`** — top-level screen kinds: `tabs`, `submenu`, `view`, `root`.
- **`ViewLayout`** — the large set of view layout kinds (lists, grids, lines,
  inputs, maps, FABs, etc.).
- **Interfaces** — `IRenderable`, `IRenderableView`, `IRenderableScreen`,
  `IRenderableLabel`, `IRenderableImage`, `IRenderableAction`, their `*Modifier`
  variants, `IRenderableLocation`, `IRenderableUrl`, `IScreenSEO`, and the
  `GenUIActionHandler` callback type.

Each enum is declared as a `const` object **and** a type of the same name, so you
can use e.g. `ThemeColor.SYSTEM_BLUE` at runtime and `ThemeColor` as a type.

## Install

The `@sudobility` scope is served from **GitHub Packages** (see `.npmrc`), so npm
must be authenticated to `npm.pkg.github.com` with a token that has `read:packages`
before installing.

```bash
bun add @sudobility/genui_types
# or: npm install @sudobility/genui_types
```

## Usage

```ts
import {
  ThemeColor,
  ViewLayout,
  type IRenderable,
} from '@sudobility/genui_types';

const node: IRenderable = {
  id: 'hello',
  view: {
    layout: ViewLayout.LINE_TITLE,
    title: {
      text: 'Hello',
      modifier: { color: ThemeColor.SYSTEM_BLUE },
    },
  },
};
```

## Development

The repo uses **Bun** as the package manager/runner (`bun.lock` is committed) and
scripts shell out to the standard TS toolchain.

```bash
bun install          # install deps (needs GitHub Packages auth, see Install)

bun run typecheck    # tsc --noEmit against tsconfig.json
bun run lint         # eslint src
bun run lint:fix     # eslint src --fix
bun run format       # prettier --write src
bun run format:check # prettier --check src
bun run test:unit    # vitest run (one-shot)
bun run test:watch   # vitest (watch mode)
bun run build        # tsc -p tsconfig.esm.json -> dist/ (ESM + .d.ts + maps)
bun run clean        # rimraf dist
bun run dev          # tsc --watch

bun run verify       # typecheck + lint + test:unit + build (the full gate)
```

### Build output

`build` compiles with `tsconfig.esm.json` to `dist/`, emitting ESM JavaScript,
`.d.ts` declarations, and source/declaration maps. The published package exposes
`dist/index.js` (`main`/`import`) and `dist/index.d.ts` (`types`); only `dist/**`
is published (`files` in `package.json`).

## Testing

Tests use **Vitest** and live beside the source (`src/*.test.ts`). There is no
separate Vitest config file; defaults are used. Run `bun run test:unit` for CI-style
one-shot runs or `bun run test:watch` while developing.

## CI/CD & publishing

`.github/workflows/ci-cd.yml` runs on pushes/PRs to `main` and `develop` via the
shared reusable workflow `johnqh/workflows/.github/workflows/unified-cicd.yml`, with
`publish-target: github` (publishes to GitHub Packages). `prepublishOnly` runs
`clean` + `verify` before any publish.

## License

BUSL-1.1 — © Sudobility.
