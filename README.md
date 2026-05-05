# katex

A localized fork of [KaTeX/KaTeX](https://github.com/KaTeX/KaTeX) intended for git-based source consumption without an npm build step.

Upstream KaTeX commits source only — `katex.ts`, `src/styles/*.scss`, `fonts/`. The JS+CSS dist artifacts live exclusively in the published npm tarball, so any git-only fetcher (or anyone preferring not to run upstream's webpack+sass-loader chain) gets nothing it can import out of the box.

This fork keeps upstream's full history and adds three local modifications on top so consumers can use it as a plain git dependency without an `exports` override or a build step:

- **`katex.min.css`** at the repo root, carried over verbatim from a prior upstream npm publish.
- **`package.json#exports`** rewritten to source paths (`./katex.ts`, `./contrib/*/<name>.js`, `./katex.min.css`) instead of `./dist/...`, so `import katex from "katex"` resolves to `./katex.ts` directly.
- **`__VERSION__`** inlined as a `const` in `katex.ts`. Upstream's webpack normally substitutes the placeholder at build time; consumers loading the source directly don't run webpack.

## Use

Any tool that fetches by git URL and follows the `exports` field works. With Bun:

```json
"dependencies": {
  "katex": "github:rmst/katex#<sha>"
}
```

Then `import katex from "katex"` resolves to `./katex.ts`. Bun loads `.ts` natively; bundlers with TypeScript support (Bun's bundler, esbuild, etc.) inline it into a browser bundle.

CSS lives at `node_modules/katex/katex.min.css`, fonts at `node_modules/katex/fonts/` (the CSS uses relative `url(fonts/…)`).

## Bumping upstream

1. Rebase or cherry-pick the fork's HEAD commit onto the new upstream commit.
2. Refresh `katex.min.css` from a published `katex@<version>` npm tarball — SCSS rebuild via webpack+sass-loader is upstream's job and out of scope here.
3. Update the `__VERSION__` constant in `katex.ts` to match.
4. Force-push.
