# Michael Neeley’s portfolio

https://redreceipt.github.io/

A handwritten HTML + CSS portfolio. No JavaScript, framework, package manager,
or tracking. IBM Plex Mono is loaded from Google Fonts. The homepage is served
as written.

## Preview

From this directory:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Open http://127.0.0.1:8000/. No install or build step is needed for the portfolio.

## Update the gallery

Edit the project cards in `index.html` and styles in `portfolio.css`.
Keep the selection small: original public projects with recent development,
useful documentation, and something meaningful to inspect or try. Do not
include private repositories or present upstream forks as original work.

The current selection is Lamplight and Animal Mania, both active in October 2026.
Links and project descriptions are deliberately curated, not fetched in the
browser. Revisit the selection when projects change; there is no automatic sync.

`assets/animal-mania.webp` is a real gameplay capture from animalmania.app,
resized to 800px wide and encoded as WebP. The Lamplight preview is an HTML
workflow diagram, not a screenshot or claimed execution output. Keep images
local, specify dimensions, and keep the total homepage payload below 100 KB.

## Publishing and older content

GitHub Pages already publishes `master` at the repository root. Merging the
portfolio PR will update the homepage; pushing a feature branch does not
publish it. No Pages settings changes are needed.

The original Jekyll configuration, layouts, posts, and conference pages remain
to preserve older URLs. The malformed collection default in `_config.yml`
has been fixed so Pages can build again. Jekyll processes the legacy Markdown
and Sass only; the portfolio has no front matter, Liquid, or generated content.
Markdown pages use `style.scss` and the same monospace palette as the homepage.
The Python preview shows the new portfolio, not the rendered legacy pages.

The original Jekyll Now license is retained in `LICENSE`.
