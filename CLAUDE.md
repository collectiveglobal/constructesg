# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Marketing site for ConstructESG, served at https://constructionesg.ca (see `CNAME`). It is hand-edited static HTML built on the purchased "Fastland" Bootstrap 5 + jQuery landing-page template. There is no package manager, build step, linter or test suite.

## Commands

- **Preview locally:** `python3 -m http.server 8000` from the repo root, then open http://localhost:8000/index.html. Don't open files via `file://`, because internal links are root-relative (`/index.html`, `/blogs.html`) and break there.
- **Deploy:** GitHub Pages (legacy build) publishes the `main` branch root. **Every push to `main` goes live immediately.** `404.html` is served as the custom 404 page.

## Which pages are real

- `index.html` is a single-page landing site. Its nav and footer are in-page anchors: `#about`, `#uses`, `#benefits`, `#blog`, `#contact`.
- The blog: `blogs.html` is the "Learn" listing page, and the three article pages are `blockchain-in-construction.html`, `traceability-in-construction.html` and `carbon-footprint-in-construction.html`. `blog-details.html` is only a meta-refresh redirect to `blogs.html`, kept because it was the old public URL for the blog cards.
- `404.html`, `pricing.html`, `faq.html`, `contact-1.html`, `sign-up.html`, `terms-page.html` and `coming-soon.html` are **unmodified template pages**. They still have "Fastland" titles, lorem-style copy and navs linking to about 40 template pages that don't exist. Nothing on the real site links to them. Don't copy markup or nav from them. Use `index.html` or an article page as the source.

## Architecture notes

- **No includes.** Each page carries its own full copy of the `<head>`, header and footer. A change to the nav, footer, stylesheets or scripts has to be made in every real page by hand. The subpages link back with `/index.html#section`, while `index.html` uses bare `#section` anchors.
- **GA4.** The Google tag (`G-QSHL075L8Y`) is the first thing in every page's `<head>`. Keep it on any new page.
- **CSS.** `css/main.css` (about 16k lines) and `css/bootstrap.css` are template output; `css/maps/` points to SCSS sources that aren't in this repo. Put all site changes in `css/custom-styles.css`, which loads last. It holds the blog article styles (`.blog-details--article`, `.article-body`, `.article-aside`, `.blog-listing__excerpt`), layered on top of the template's `.blog-details` and `.blogs-post` classes.
- **JS load order matters.** `js/custom.js` initializes every plugin (nice-select, counterUp, fancybox, slick, AOS, isotope, skill bars) inside one `$(document).ready`.
  - If a page leaves out one of the plugin scripts, that call throws and the rest of the block never runs, including `AOS.init()`. Elements with `data-aos` then stay invisible.
  - The `#loading` preloader is removed only by `custom.js` on window load, so a JS error leaves it covering the page.
  - Every page should load the same script list, in the same order, as `index.html`.
  - `custom.js` uses `$(window).load(...)`, which only works because `jquery-migrate` is loaded alongside jQuery 3.3.1. Don't remove migrate.
- **Contact form.** The live contact form is a Google Forms iframe embedded in `index.html#contact`. `plugins/php/mailer.php` and `plugins/php/ajax_contact.js` are broken template leftovers: they aren't referenced anywhere, `mailer.php` contains another company's email addresses, and GitHub Pages can't run PHP anyway.
- The theme-mode switcher script is commented out. Pages are fixed to `data-theme="light"`.

## Blog conventions

- Articles are **evergreen**: no publish dates, no author bylines, no time-bound claims such as "next year" or specific deadlines. Show a read time (about 200 words per minute) instead of a date. Use Canadian spelling (labour, aluminium).
- To add an article:
  1. Copy an existing article page.
  2. Update `<title>`, the meta description, `canonical`, and the `og:*` tags. Their URLs use `https://constructionesg.ca/`.
  3. Add a card to `blogs.html` and to the `#blog` section of `index.html`.
  4. Add a link to it in the "More to read" sidebar of the other articles.
- The article images in `image/jpg/constructesg-*.jpg` are small (324×464 portrait), so they sit in the sidebar at close to native size rather than as full-width heroes.
