# Portfolio

A small static site: About, Blog, Resume, Projects. Built by hand, no
framework or static-site generator — the point of this repo is learning
how these pieces work under the hood.

## Structure

- `index.html`, `resume.html`, `projects.html` — hand-written pages.
  Nav/footer markup is duplicated across them (only 3-4 pages, so
  that's an acceptable trade-off rather than reaching for a templating
  system).
- `assets/css/style.css` — one shared stylesheet for the whole site.
- `content/blog/*.md` — blog posts, written in Markdown with a small
  front-matter header (title/date/description).
- `blog/*.html` — generated from `content/blog/*.md`. Don't hand-edit
  these; run `npm run build` instead.
- `scripts/build-blog.js` — the build script: reads the Markdown
  posts, renders them through one shared template, and writes
  `blog/index.html` (the post list) plus one HTML file per post.

## Working locally

```
npm install        # first time only
npm run build       # regenerate blog/ from content/blog/
```

To preview, just open the HTML files in a browser, or run any static
file server, e.g. `npx serve .`.

## Deploying

Plain GitHub Pages, serving straight from the repo — no CI build step
(yet). Generated blog HTML is committed, so Pages just serves the
files as they exist in the repo.
