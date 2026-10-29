# Personal blog

A Jekyll blog using the Minimal theme (`jekyll-theme-minimal`), matching https://github.com/cdtran/cdtran.github.io, with an automatic GitHub Pages deployment workflow. Minimal is different from Minima. Local home, page, and post layouts add blogging features while inheriting Minimal's design.

## Preview locally

Install Ruby 3.3 (the macOS system Ruby is too old), then run:

```sh
gem install bundler
bundle install
bundle exec jekyll serve
```

Open http://localhost:4000. Restart Jekyll after changing `_config.yml`.
Commit the generated `Gemfile.lock` to keep dependency versions consistent.

## Publish on GitHub Pages

1. Use the existing `cdtran/cdtran.github.io` repository. It currently publishes from the root of `main`; the workflow below requires switching its Pages source to GitHub Actions.
2. Upload this folder's contents, including `.github/workflows/pages.yml`, to the repository's `main` branch.
3. Open the repository's **Settings → Pages** and set **Source** to **GitHub Actions**.
4. Open **Actions → Publish blog to GitHub Pages → Run workflow**. Later pushes to `main` publish automatically.
5. Find the live URL under **Settings → Pages** or the workflow's deployment.

The workflow automatically sets the site's domain and path for either repository type. For production builds outside GitHub Actions, set `url` to `https://YOUR-USERNAME.github.io` and `baseurl` to `""` for an account site or `/personal-blog` for a project site.

## Make it yours

- Edit the title, author, and description in `_config.yml`.
- Replace the introduction in `index.md` and biography in `about.md`.
- Edit or delete the starter post in `_posts/`.
- Optionally add `github_username` and `email` to `_config.yml` to show them in the footer. Only add contact details you want public.

## Write a post

Create `_posts/YYYY-MM-DD-post-title.md`:

```markdown
---
title: "My next post"
date: 2026-10-29 09:00:00 -0700
categories: personal
---

Write your post in Markdown here.
```

Use the publication date you want. Future-dated posts stay hidden until a build runs after that date; this workflow runs on pushes or manual dispatch, not on a schedule.

## Build

```sh
JEKYLL_ENV=production bundle exec jekyll build
```

Generated files are written to `_site/` and excluded from Git.

References: [Jekyll on GitHub Actions](https://jekyllrb.com/docs/continuous-integration/github-actions/) · [Minimal](https://github.com/pages-themes/minimal)
