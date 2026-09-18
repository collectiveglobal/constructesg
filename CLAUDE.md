# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Marketing site for ConstructESG, served at https://constructionesg.ca (see `CNAME`). It is hand-edited static HTML built on the purchased "Fastland" Bootstrap 5 + jQuery landing-page template. There is no package manager, build step, linter or test suite.

## Commands

- **Preview locally:** `python3 -m http.server 8000` from the repo root, then open http://localhost:8000/index.html. Don't open files via `file://`, because internal links are root-relative (`/index.html`, `/blogs.html`) and break there.
- **Deploy:** GitHub Pages (legacy build) publishes the `main` branch root. **Every push to `main` goes live immediately.** HTTPS is enforced. `_config.yml` only excludes `README.md` and `CLAUDE.md` from the Jekyll build, so they aren't published.

## Which pages are real

- `index.html` is the landing page. It has sections `#about`, `#benefits`, `#uses`, `#blog` (three featured articles) and `#contact`.
- `about.html` is the About page. Its copy is deliberately limited to claims the homepage already makes: the 10+ years in construction administration, the mission, the three audiences, the seven capabilities and the three-step onboarding process.
- The blog: `blogs.html` is the Blog listing page, and there are eight root-level article pages. `README.md` has the table of articles and their reading order. `blog-details.html` is only a meta-refresh redirect to `blogs.html`, kept because it was the old public URL for the blog cards.
- `404.html` is served at any missing URL, including nested paths, so **every asset path in it must be root-relative** (`/css/…`, `/image/…`, `/js/…`), unlike the other pages, which use `./`.
- The original Fastland template pages, stock images, demo files and unused plugins have been deleted. Nothing left in the repo is template-only.

## Architecture notes

- **No includes.** Each page carries its own full copy of the `<head>`, header and footer. A change to the nav, footer, stylesheets or scripts has to be made in every real page by hand. Nav rules:
  - **About** and **Blog** link to `/about.html` and `/blogs.html` from every page.
  - **Use Cases**, **Benefits** and **Contact** are bare `#section` anchors on `index.html` and `/index.html#section` everywhere else.
  - The matching nav item gets the class `is-active` on the About page, the Blog page and every article, plus `aria-current="page"` on the page it links to.
- **GA4.** The Google tag (`G-QSHL075L8Y`) is the first thing in every page's `<head>`. Keep it on any new page.
- **CSS.** `css/main.css` (about 16k lines) and `css/bootstrap.css` are template output; `css/maps/` points to SCSS sources that aren't in this repo. Put all site changes in `css/custom-styles.css`, which loads last. It holds the page-specific styles: the blog (`.blog-details--article`, `.article-body`, `.article-aside`, `.blog-listing__excerpt`), the About page (`.about-*`), the 404 page (`.not-found*`) and the nav (`.is-active`). Brand colours are green `#649b82` and navy `#213764`.
- **JS load order matters.** `js/custom.js` initializes every plugin (nice-select, counterUp, fancybox, slick, AOS, isotope, skill bars) inside one `$(document).ready`.
  - If a page leaves out one of the plugin scripts, that call throws and the rest of the block never runs, including `AOS.init()`. Elements with `data-aos` then stay invisible.
  - The `#loading` preloader is removed only by `custom.js` on window load, so a JS error leaves it covering the page.
  - Every page should load the same script list, in the same order, as `index.html`.
  - `custom.js` uses `$(window).load(...)`, which only works because `jquery-migrate` is loaded alongside jQuery 3.3.1. Don't remove migrate.
- **Contact.** The only contact route is the Google Forms iframe in `index.html#contact`. The contact block lists just the Toronto HQ. Email addresses and a phone number were removed on purpose: `constructesg.com` has no DNS, and the phone number was a template placeholder. Don't add contact details unless the user supplies real ones.
- The theme-mode switcher has been removed. Pages are fixed to `data-theme="light"`.
- **No placeholders, no dead files.** Don't publish placeholder copy or `href="#"` links, and keep `image/` and `plugins/` limited to files that are actually referenced.

## Blog conventions

- Articles are **evergreen**: no publish dates, no author bylines, no time-bound claims such as "next year" or specific deadlines. Each shows a category (Getting started / Environmental / Social / Governance / Technology) and a read time (words ÷ 200, rounded up) instead of a date. Use Canadian spelling (labour, aluminium, program).
- Keep claims about ConstructESG within what `index.html` says the product does.
- Articles link to each other in three places, all maintained by hand:
  - a "More to read" sidebar listing three related articles;
  - a "Next article" link that follows the reading order in `README.md`;
  - links in the body text.
  Adding an article means updating neighbouring pages too. Follow the "Adding an article" checklist in `README.md`.
- Cover images are small portrait JPEGs with a width-to-height ratio of about 0.7 (`image/jpg/constructesg-<slug>.jpg`). They sit in the sidebar near native size rather than as full-width heroes.
- `<img>` tags in blog cards carry `width`/`height` attributes. `.blogs-post img { height: auto; }` in `custom-styles.css` is what stops them rendering squashed, so keep that rule.
