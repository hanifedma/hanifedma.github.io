# hanifedma.com

Source for my personal site — a home page, a portfolio, and a slide-style version of the
portfolio prepared for interviews. Served by GitHub Pages from the `main` branch and mapped
onto `hanifedma.com` by the [`CNAME`](CNAME) file.

**Live:** <https://hanifedma.com>

## Layout

| Path | What it is |
|------|-----------|
| `index.html` | Home page — intro, links, and two looping clips from the published work |
| `portfolio/index.html` | Portfolio — research, engineering projects, and web apps |
| `portfolio_presentation/index.html` | The same material as a slide deck, plus the deck as a PDF |
| `favicon/` | Icons and the web manifest |
| `Resume_Hanif_Edma_Fauz.pdf` | The resume linked from the home page |
| `*.webm`, `*_poster.jpg` | Home page demo clips and their poster frames |

Each page is a single self-contained HTML file with its CSS and JavaScript inline. There is no
framework, no build step, and nothing to install.

## Running it locally

Any static file server will do:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Serve it over `http://` rather than opening `index.html` as a `file://` URL, so that the
theme, the lazy-loading clips, and the update check all behave as they do in production.

## How it stays light

The site is built to open quickly on a slow connection and to run on a machine with no GPU.

- **No web fonts.** The type is a monospace system stack, so nothing is downloaded to render text.
- **No render-blocking requests.** CSS and JS are inline, so a page is one request.
- **Video is deferred.** Clips carry `preload="none"` and a poster frame; an `IntersectionObserver`
  starts them only once they scroll into view and pauses them when they leave. The home page's
  ~1.4 MB of video is never fetched by a visitor who does not scroll to it, and no clip decodes
  off-screen.
- **Images are lazy and sized.** `<picture>` serves WebP with a JPEG fallback, every image
  declares `width`/`height` so nothing shifts as the page loads, and off-screen images are
  `loading="lazy"`.
- **The theme is applied before the first paint**, so there is no flash of the wrong colours,
  and transitions stay switched off until the first frame has painted.

## How visitors get the newest version

GitHub Pages serves HTML with `Cache-Control: max-age=600`, so a browser may paint a page from
its own cache for up to ten minutes without ever asking the server. Each page therefore checks,
whenever its tab becomes visible again, whether the deployed `ETag` differs from the version it
is showing — and reloads itself if it does.

The subtlety is that a page painted from the cache has to compare against the validator stored
when *that copy* was fetched, not against a fresh probe of the server: probing first would adopt
the newer version as the baseline and leave the visitor reading a stale page for as long as the
tab stayed open. The check runs only on `visibilitychange`, so an idle tab costs nothing.

Media whose contents change while keeping their filename are cache-busted with a `?v=N` query
string, and the resume link is stamped with a timestamp so it always opens the current PDF.

## Deploying

Pushing to `main` publishes the site; GitHub Pages picks it up within a minute or two.

The LaTeX sources for the resume and the portfolio PDF are kept on my machine rather than in this
repository — see [`.gitignore`](.gitignore).

## Web apps

The apps linked from the portfolio live in their own repositories:

- [CanopyDiary](https://github.com/hanifedma/canopydiary) — a local-first diary · [live](https://hanifedma.com/canopydiary/)
- [Ponder](https://github.com/hanifedma/ponder) — quotes and thoughts · [live](https://hanifedma.com/ponder/)
- [Waterline](https://github.com/hanifedma/waterline) — a water-fasting tracker · [live](https://hanifedma.com/waterline/)
- [TimberTimer](https://github.com/hanifedma/timbertimer) — a jungle focus timer · [live](https://hanifedma.com/timbertimer/)
