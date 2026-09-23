# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```sh
npm run dev              # start dev server at localhost:4321
npm run build            # production build to ./dist/
npm run preview          # preview the production build locally
npm run astro check      # type-check .astro files and content schemas
```

Run the dev server in the background so it doesn't block the session, and manage it with the matching subcommands:

```sh
astro dev --background
astro dev stop
astro dev status
astro dev logs
```

There is no test suite, linter, or formatter configured in this repo.

## Architecture

This is an Astro site (Astro 7, TypeScript strict mode via `astro/tsconfigs/strict`) built from the official blog starter, now partway through being turned into a personal portfolio.

- **Shared layout**: every page routes through [src/layouts/Layout.astro](src/layouts/Layout.astro) (`Header` + `<main>` + `Footer` + `BaseHead`), except actual blog posts which use [src/layouts/BlogPost.astro](src/layouts/BlogPost.astro) (adds the hero image/date/title block around the same Header/Footer). Pass `wide` to `Layout` for a 960px `<main>` (used by the homepage); the default is a 720px reading-width column.
- **Design tokens**: colors, spacing, and the base element styles live in [src/styles/global.css](src/styles/global.css) as CSS custom properties (`--bg`, `--surface`, `--border`, `--text`, `--text-muted`, `--accent`, `--accent-dark`, `--shadow`). Page-specific `<style>` blocks (e.g. `index.astro`'s hero/project grid) build on top of these tokens rather than hardcoding colors.
- **Content collections**: blog posts live in `src/content/blog/*.{md,mdx}` and are loaded via `glob()` in [src/content.config.ts](src/content.config.ts), which also defines the frontmatter schema (`title`, `description`, `pubDate`, `updatedDate?`, `heroImage?`). Any new post must satisfy this schema or the build fails type-checking. The collection is currently empty (placeholder starter posts were deleted) — `src/content/blog/.gitkeep` just keeps the directory from disappearing; delete it once real posts exist.
- **Routing**: file-based under `src/pages/`. `src/pages/blog/[...slug].astro` calls `getStaticPaths()` over the blog collection and renders each entry through `BlogPost.astro`, keyed by `post.id` (not `post.slug` — the glob loader's entries don't have a `.slug` field). `src/pages/blog/index.astro` and `src/pages/rss.xml.js` also read the same collection.
- **Site identity**: `SITE_TITLE`/`SITE_DESCRIPTION`/`SOCIAL_LINKS` (github/linkedin/email/whatsapp) live in [src/consts.ts](src/consts.ts) — `Header`, `Footer`, and `contact.astro` all read from here rather than hardcoding links. The `site` URL in [astro.config.mjs](astro.config.mjs) is still the placeholder `https://example.com`; update it once the site has a real domain (it feeds the sitemap, RSS, and canonical URLs).
- **Fonts**: the `Atkinson` font is registered as a local font provider in `astro.config.mjs`, backed by `.woff` files in `src/assets/fonts/`.
- **Astro whitespace gotcha**: a text node that is pure whitespace *and* contains a newline is dropped entirely by the compiler rather than collapsed to a single space — e.g. `built\n<strong>X</strong>` renders as `builtX`. When inline text wraps across lines right before/after a tag, either keep the boundary on one line or insert an explicit `{' '}`.

## Documentation

Full documentation: https://docs.astro.build

Consult these guides before working on related tasks:

- [Adding pages, dynamic routes, or middleware](https://docs.astro.build/en/guides/routing/)
- [Working with Astro components](https://docs.astro.build/en/basics/astro-components/)
- [Using React, Vue, Svelte, or other framework components](https://docs.astro.build/en/guides/framework-components/)
- [Adding or managing content](https://docs.astro.build/en/guides/content-collections/)
- [Adding styles or using Tailwind](https://docs.astro.build/en/guides/styling/)
- [Supporting multiple languages](https://docs.astro.build/en/guides/internationalization/)
