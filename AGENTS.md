# AGENTS.md

## Project Purpose

Phoenix Academy is a HugoBlox-based Persian educational website focused on Agentic AI, robotics, UAV systems, ROS 2, autonomous systems, and practical engineering education.

The goal is to keep the site professional, research-oriented, beginner-friendly, and useful for students who are learning modern robotics and AI workflows.

## Repository Layout

- `content/blog/`: Blog posts and article bundles.
- `content/blog/<post>/`: A blog post folder containing `_index.md` or `index.md` plus local images.
- `config/_default/`: Hugo and HugoBlox configuration.
- `assets/`, `layouts/`, `static/`: Site styling, layout overrides, and static assets when present.
- `.github/workflows/`: GitHub Pages deployment workflows.

## Working Agreements

- Read relevant existing posts before editing content so tone, metadata, image usage, and formatting stay consistent.
- For Persian RTL posts, wrap inline English technical terms with `<bdi dir="ltr">...</bdi>`.
- Prefer local post assets such as `1.jpg`, `2.png`, etc. referenced through Hugo `figure` shortcodes.
- Keep blog posts compact, educational, and structured with clear headings.
- Do not turn fundamentals posts into long project tutorials unless explicitly requested.
- Preserve user-written drafts and improve them rather than replacing the intent.

## HugoBlox Content Rules

For blog posts, include front matter when missing:

```yaml
---
title: "..."
summary: "..."
date: YYYY-MM-DD
authors:
  - admin
tags:
  - ...
categories:
  - ...
lang: fa
image:
  filename: 1.jpg
  caption: "..."
  alt_text: "..."
thumbnail: 1.jpg
---
```

Use shortcodes for local figures:

```hugo
{{< figure src="2.jpg" caption="..." >}}
```

Avoid unverified shortcodes or diagrams unless the local Hugo build confirms they render correctly. If Mermaid support is uncertain, use a Markdown table instead.

## Commands

From the repository root:

```bash
pnpm install
pnpm run dev
pnpm run build
```

The project expects Hugo Extended. Check with:

```bash
hugo version
```

## Verification

Before finishing substantial site edits:

- Run `pnpm run build` when Hugo is available.
- Check that images are real image files, not downloaded HTML pages.
- Confirm front matter, figure paths, Persian/English mixed text, and links are valid.
- Review `git status --short` and mention changed files clearly.

## Safety And Editing

- Never delete or rewrite unrelated user changes.
- Do not run destructive git commands unless explicitly requested.
- Ask before installing system-wide dependencies.
- Prefer local installs under `~/.local/bin` when fixing developer tooling.
- For external images, preserve source links and license/attribution notes.

## Good Prompt Shape For This Repo

When asking Codex for work, prefer:

- **Goal**: What page, post, feature, or fix is needed?
- **Context**: Mention relevant files such as `content/blog/.../_index.md`.
- **Constraints**: Tone, audience, length, image count, tooling, language.
- **Done when**: Build passes, images render, metadata is complete, etc.

## Candidate Skills To Create Later

Create repo skills under `.agents/skills/`

Good first skills for this website:

- `persian-hugoblox-post`: Polish or create Persian HugoBlox blog posts.
- `blog-image-curation`: Download, rename, attribute, and place blog images.
- `hugo-preview-debug`: Diagnose HugoBlox preview/build errors.
- `robotics-article-research`: Gather sources for robotics, UAV, ROS 2, and AI posts.
