# Bells and Thistles

Source code of [bellsandthistl.es](https://bellsandthistl.es/), the personal site of Nadezhda Krasnushkina, a technical writer. The site has a blog, a resume, and a list of portfolio projects.

## Stack

- [Hugo](https://gohugo.io/) v0.165.0, **extended** edition.
- [Cactus](https://github.com/monkeyWzr/hugo-theme-cactus) theme, added as a git submodule in `themes/cactus`.
- Hosting: GitHub Pages, built and deployed with GitHub Actions.

## Getting started

Clone the repository together with the theme submodule:

```bash
git clone --recurse-submodules https://github.com/Cluaran3/bells-and-thistles.git
```

If you've already cloned it without the flag, `themes/cactus` will be empty. Fetch the theme with:

```bash
git submodule update --init
```

Start the local server:

```bash
hugo server       # published content only
hugo server -D    # include drafts
```

## Project structure

Only the files that belong to this site are listed. Everything else comes from the theme.

```
content/
  blog/             Blog posts. _index.md sets type = "posts" for the whole section.
  resume/           Page bundle: the resume page and its downloadable PDF.
  projects.md       Portfolio projects.
layouts/
  index.html        Home page.
  partials/         Overrides of the theme's partials.
  shortcodes/       Custom shortcodes.
static/
  css/custom.css    Style overrides on top of the theme.
  images/           Logos and favicon.
hugo.toml           Site configuration.
```

## Customizations over Cactus

The theme files in `themes/cactus/` are never edited directly. To change a template, copy it to the same path under `layouts/` and edit the copy: Hugo looks in the site's `layouts/` before the theme's. This keeps theme updates painless and makes every change visible in one place.

- **Per-section logos.** `layouts/partials/header.html` reads a `logo` parameter from front matter, so each section can have its own header image. The blog sets it once for all posts through `cascade`.
- **Previous/next navigation.** `layouts/partials/page_nav.html` uses `.PrevInSection` and `.NextInSection`, so the arrows on a post move between blog posts only, not across all pages of the site.
- **Home page.** `layouts/index.html` filters the post feed by section instead of type (the blog's `cascade` changes the type of every post to `posts`) and adds an About block from `layouts/partials/optional-about.html`.
- **Projects page.** A regular content page instead of the theme's built-in projects widget, which is turned off with `showProjectsList = false`.
- **Download shortcode.** `layouts/shortcodes/download.html` renders a download button for a file stored next to the page in its bundle:
  ```
  {{< download "file.pdf" "Download PDF" >}}
  ```
  If the file isn't found, the build fails with an error instead of producing a broken link.
- **Post lists.** `static/css/custom.css` keeps the date column at a fixed width, so long post titles wrap without shifting the dates.

## Writing a new post

```bash
hugo new content blog/<slug>.md
```

New posts are created with `draft = true` and live on a separate local `drafts` branch until they're ready. To publish one, bring it over to `main`, set `draft = false`, and commit:

```bash
git switch main
git checkout drafts -- content/blog/<slug>.md
```

## Deployment

The site is deployed to GitHub Pages by the workflow in [`.github/workflows/hugo.yml`](.github/workflows/hugo.yml). It runs on every push to `main` and can also be started manually from the **Actions** tab.

The workflow has two jobs:

1. **build**: installs Hugo extended (pinned to the same version as in [Stack](#stack)), checks out the repository with the theme submodule, builds the site, and uploads `public/` as a Pages artifact.
2. **deploy**: publishes the artifact to GitHub Pages.

A few things to know:

- **Base URL.** The workflow passes `--baseURL` from `actions/configure-pages`, so the build always matches the address GitHub Pages actually serves, whether it's the default `*.github.io` URL or the custom domain.
- **Drafts.** The build runs without `-D`, so posts with `draft = true` never reach the live site.
- **Upgrading Hugo.** Update `HUGO_VERSION` in the workflow together with your local installation to keep local and production builds identical.

### Custom domain

The site is served at `bellsandthistl.es`. The domain is set in the repository's **Settings → Pages → Custom domain**, not in a `CNAME` file, because the site is deployed with GitHub Actions. DNS records point to GitHub Pages:

| Type | Name | Value |
|---|---|---|
| A | `@` | `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` |
| AAAA | `@` | `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` |
| CNAME | `www` | `cluaran3.github.io` |

The domain is also verified in the GitHub account settings with a TXT record, which prevents other repositories from claiming it.

## License

The Cactus theme is distributed under the [MIT License](https://github.com/monkeyWzr/hugo-theme-cactus/blob/master/LICENSE).

The site content (blog posts, resume, images) is © Nadezhda Krasnushkina. All rights reserved.
