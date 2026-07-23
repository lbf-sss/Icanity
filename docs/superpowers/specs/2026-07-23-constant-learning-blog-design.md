# Constant Learning Blog Design

## Context

`lbf-sss/Icanity` is the Fuwari Astro template. The target reference is
`https://xuchenhui.cc`, which uses the same family of visual conventions:
card-based navigation, a desktop profile sidebar, category and tag widgets,
and a single-column article feed. The blog identity is Evander Liu and the
theme is `Constant Learning`. Articles are bilingual Chinese/English.

The implementation should reproduce the reference structure without copying
its personal content, images, or contact details. The phrase previously
removed from the profile copy will not be reintroduced.

## Goals

- Make the existing Fuwari site read as Evander Liu's personal blog.
- Keep the reference site's visual hierarchy: sticky card navigation, profile
  sidebar, category/tag widgets, and one main article column.
- Use `Constant Learning` as the site title/theme without adding a long bio
  sentence to the profile.
- Support Chinese and English post metadata and body content through Fuwari's
  existing Markdown content collection.
- Deploy the project to GitHub Pages at
  `https://lbf-sss.github.io/Icanity/`.
- Preserve Fuwari's existing light/dark mode, search, archive, RSS, table of
  contents, code blocks, and responsive behavior.

## Non-goals

- No custom CMS or database.
- No comment service or external analytics in this first pass.
- No new animation system or slide-deck runtime.
- No custom domain setup.
- No replacement of the Fuwari layout with a new framework.
- Use the existing Fuwari demo avatar and only the repository GitHub link until
  the owner provides a personal avatar or additional social destinations.

## Visual Design

### Identity

- Site title: `Evander Liu · Constant Learning`
- Subtitle: `Constant Learning`
- Language: `zh_CN`
- Theme hue: `345` (rose/pink, matching the reference site's accent)
- Banner: disabled for the first pass, matching the reference's content-first
  homepage.
- Profile name: `Evander Liu`
- Profile bio: `Constant Learning`
- Temporary avatar: the Fuwari demo avatar already in the repository.

### Layout

Desktop uses Fuwari's existing two-column `MainGridLayout`:

```text
sticky navbar
┌───────────────┬────────────────────────────────────┐
│ profile       │ latest posts                       │
│ categories    │ post card                          │
│ tags          │ post card                          │
│               │ post card                          │
└───────────────┴────────────────────────────────────┘
```

The right-hand content remains a single vertical article stream. The left
sidebar is the navigation/profile context, not a second article feed. On
mobile, the sidebar collapses above the article stream and the compact nav menu
is used.

### Navigation and widgets

Use the existing Fuwari components and configuration:

- Navigation: Home, Archive, About, and the owner's GitHub repository.
- Controls: existing search, theme color, and light/dark controls.
- Profile widget: name, short theme bio, avatar, and only links with known
  destinations.
- Categories: `Technology`, `Learning`, and `Thinking` once posts populate
  those categories.
- Tags are derived from post frontmatter and remain filterable through the
  existing archive UI.

## Content Model

Posts remain Markdown files in `src/content/posts/` with the existing Fuwari
frontmatter schema:

```yaml
---
title: Learning in Public / 在公开中学习
published: 2026-07-23
description: A short bilingual excerpt.
tags: [learning, reflection, bilingual]
category: Learning
draft: false
---
```

The first content pass will remove the template's public demo posts so the
homepage does not present Fuwari sample copy. It will add one published starter
post named `constant-learning.md` with the title `Constant Learning / 持续学习`
and a short bilingual introduction specific to Evander Liu. Additional posts
remain out of scope until the owner supplies their content.

## Deployment

`astro.config.mjs` will use:

```js
site: "https://lbf-sss.github.io",
base: "/Icanity",
```

Add `.github/workflows/deploy.yml` using the official Astro GitHub Action:

1. Checkout `main` on push and manual dispatch.
2. Build with `withastro/action`.
3. Upload the generated site to the `github-pages` environment with
   `actions/deploy-pages`.

After the workflow is committed, GitHub repository settings must select
`Settings -> Pages -> Source -> GitHub Actions`.

## Data Flow

```text
Markdown post
    -> Astro content collection
    -> PostCard / ArchivePanel / PostPage
    -> static HTML + Pagefind index + RSS
    -> GitHub Pages deployment
```

There is no runtime server dependency for reading posts. A failed content
schema check or production build should stop deployment rather than publish a
partial site.

## Verification

Before implementation is considered complete:

- `pnpm install` completes with the repository's pinned pnpm version.
- `pnpm check` passes.
- `pnpm build` generates `dist/` and the Pagefind index.
- `pnpm preview` shows the home, archive, about, post, RSS, and search paths.
- Desktop verification confirms the sidebar + single article column layout.
- Mobile verification confirms the sidebar collapse and navigation menu.
- Light mode and dark mode keep readable contrast.
- The `/Icanity` base path is present in CSS, scripts, links, sitemap, and RSS.
- The GitHub Pages workflow succeeds on `main`.

## Change Boundaries

The first implementation should touch only:

- `src/config.ts` for site, nav, profile, language, and theme values.
- `astro.config.mjs` for `site` and `base`.
- `src/content/posts/` for the owner's initial articles and removal of sample
  public copy.
- `.github/workflows/deploy.yml` for GitHub Pages deployment.
- `README.md` only if repository-specific run/deploy instructions are needed.

Existing Fuwari components and styles remain the source of truth unless visual
verification identifies a concrete mismatch with the approved reference
structure.
