# Michael Neeley’s portfolio

https://redreceipt.github.io/

Plain HTML and CSS. No framework, JavaScript, package manager, templates, or
build step. The monospace theme supports light and dark system preferences.
IBM Plex Mono is loaded from Google Fonts, with system monospace fallbacks.

## Preview

From this directory:

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

Open http://127.0.0.1:8000/. This serves the same files GitHub Pages publishes.

## Edit

- Update the curated project cards in `index.html`.
- Edit the shared theme in `portfolio.css`.
- Write articles directly as HTML in `<slug>/index.html`, using the shared
  stylesheet. No Markdown conversion or generated layouts are involved.
- Keep images local and give them dimensions and descriptive alt text.

`assets/animal-mania.webp` is a real gameplay capture from animalmania.app.
The Lamplight preview is an HTML workflow diagram, not a screenshot.

## Publish

GitHub Pages publishes `master` from the repository root. Feature branches and
pull requests do not publish to the live site. `.nojekyll` disables Jekyll
processing: HTML, CSS, and assets are served directly, with no site build.

The old Jekyll starter content has been removed. Its placeholder URLs are not
preserved. `LICENSE` retains the original license notice for attribution only;
it is not a dependency.
