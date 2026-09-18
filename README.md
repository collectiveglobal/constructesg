# ConstructESG website

Marketing site for **ConstructESG**, a blockchain-backed platform that helps construction companies record materials, carbon, labour and vendor data and turn it into ESG reports. The site is live at **https://constructionesg.ca**.

The site is plain static HTML, CSS and jQuery. There is no build step, package manager or test suite, so the files in this repository are exactly what gets served.

- [Quick start](#quick-start)
- [Deployment](#deployment)
- [Repository layout](#repository-layout)
- [Pages](#pages)
- [How the pages are put together](#how-the-pages-are-put-together)
- [The blog](#the-blog)
- [Known issues and cleanup backlog](#known-issues-and-cleanup-backlog)

## Quick start

Serve the repository root with any static file server:

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000/index.html.

Use a server rather than opening files directly. Internal links are root-relative (`/index.html`, `/blogs.html`), so they break under `file://`.

## Deployment

The site is hosted on **GitHub Pages** from the `main` branch, repository root.

- **Every push to `main` is a production deploy.** Pages rebuilds in about a minute.
- The custom domain comes from the `CNAME` file (`constructionesg.ca`). Don't delete or rename it.
- `404.html` is served for any URL that doesn't exist.
- Pages runs the files through Jekyll (there is no `.nojekyll` file), so Markdown files such as this README are also published on the site (for example `/README.md`).

To check the latest build:

```sh
gh api repos/collectiveglobal/constructesg/pages/builds/latest --jq '.status, .commit, .error.message'
```

## Repository layout

```
.
├── index.html                  Homepage (landing page with anchor sections)
├── about.html                  About page
├── blogs.html                  Blog page listing every article
├── *-in-construction.html      Blog articles (plus a few with other slugs; see "The blog")
├── blog-details.html           Redirect to blogs.html (old blog URL)
├── 404.html, pricing.html, …   Unmodified template pages (see "Pages")
├── css/
│   ├── bootstrap.css           Bootstrap 5 (template build)
│   ├── main.css                Template theme styles, ~16k lines; treat as vendor code
│   └── custom-styles.css       ConstructESG overrides; make your CSS changes here
├── js/
│   ├── bootstrap.bundle.js
│   ├── menu.js                 Mobile menu behaviour
│   └── custom.js               Initializes every plugin (see "JavaScript")
├── plugins/                    jQuery, AOS, slick, fancybox, counter-up, isotope, …
├── fonts/                      Font Awesome 5, the template's icon fonts, typography CSS
├── image/
│   ├── png/                    Logos and favicon (ConstructESG-*.png)
│   ├── jpg/constructesg-*.jpg  Blog cover images
│   ├── jpg/, home-3/           Homepage photography (constructesg-*, gov, co2-est, …)
│   └── home-1 … home-8/        Template stock images, used only by template pages
├── CNAME                       Custom domain for GitHub Pages
└── CLAUDE.md                   Notes for AI coding assistants
```

## Pages

| File | What it is | Status |
| --- | --- | --- |
| `index.html` | Homepage. Sections: `#about` (platform features), `#benefits`, `#uses`, `#blog` (three featured articles), `#contact` | Live, maintained |
| `about.html` | About page: why ConstructESG exists, who it's for, what the platform covers, and how onboarding works | Live, maintained |
| `blogs.html` | Blog page listing all articles | Live, maintained |
| Article pages | Eight evergreen articles (see [The blog](#the-blog)) | Live, maintained |
| `blog-details.html` | Meta-refresh redirect to `blogs.html`, kept because it was the blog's old public URL | Redirect only |
| `404.html`, `pricing.html`, `faq.html`, `contact-1.html`, `sign-up.html`, `terms-page.html`, `coming-soon.html` | Pages from the original template | **Not yet adapted.** They still say "Fastland", contain placeholder copy and link to template pages that don't exist. Nothing on the real site links to them, except that `404.html` is shown for broken URLs. |

## How the pages are put together

The site began as the **Fastland** landing-page template (Bootstrap 5 + jQuery). The homepage and the blog have been rebuilt with ConstructESG content; the template's styling and plugins remain underneath.

### Shared header, footer and `<head>`

There are no includes or templates. Every page contains its own full copy of the `<head>`, header and footer, so any change to the navigation, footer, stylesheets or scripts must be made **in every maintained page**.

- **About** and **Blog** are separate pages (`/about.html` and `/blogs.html`) on every page.
- **Use Cases**, **Benefits** and **Contact** are homepage sections. They're plain anchors on `index.html` (`#uses`) and point back to the homepage from every other page (`/index.html#uses`).
- On the About and Blog pages, and on every article, the matching nav item gets the class `is-active` (green, underlined on desktop). The page the link actually points to also gets `aria-current="page"`.

### CSS

Stylesheets load in this order: `bootstrap.css`, the icon and typography fonts, plugin CSS, `main.css`, and finally `custom-styles.css`.

- `main.css` and `bootstrap.css` are compiled template output; the SCSS sources aren't in this repository.
- Put all site-specific styling in **`css/custom-styles.css`**, which loads last and overrides the rest.
- `custom-styles.css` also holds the page-specific styles:
  - **Blog:** `.blog-details--article`, `.article-body`, `.article-aside`, `.blog-listing__excerpt` and `.blog-area__more`. They build on the template's `.blog-details`, `.blogs-post` and `.sidebar-area` classes.
  - **About page:** everything prefixed `.about-`.
  - **Nav:** `.nav-link-item.is-active`.
- Brand colours: green `#649b82` (the template calls it `electric-violet-2`) and navy `#213764` for headings.

### JavaScript

`js/custom.js` starts every plugin (nice-select, counterUp, fancybox, slick, AOS, skill bars, isotope) inside a single `$(document).ready`. Two consequences:

1. **Every page must load the full plugin script list, in the same order as `index.html`.** If one plugin is missing, its init call throws and the rest of the block never runs. That includes `AOS.init()`, so any element with a `data-aos` attribute stays invisible.
2. **The `#loading` preloader on the homepage is removed only by `custom.js`**, on window load. A JavaScript error leaves the preloader covering the page.

`custom.js` also uses `$(window).load(...)`, which was removed in jQuery 3. It works only because `jquery-migrate` loads alongside jQuery 3.3.1, so don't remove `jquery-migrate`.

The template's light/dark theme switcher is disabled (its script is commented out), and pages are fixed to `data-theme="light"`.

### Analytics

Every page starts its `<head>` with the Google Analytics 4 tag, ID **`G-QSHL075L8Y`**. Include it on any new page.

### Contact form

The homepage contact form is an embedded **Google Form** (iframe in `#contact`), so submissions arrive in Google Forms. `plugins/php/mailer.php` and `plugins/php/ajax_contact.js` are template leftovers: they aren't used, they don't work, and GitHub Pages can't run PHP anyway.

## The blog

### Articles

| Article | File | Category |
| --- | --- | --- |
| What ESG means for construction companies | `esg-in-construction.html` | Getting started |
| Getting ready for ESG reporting: a step-by-step plan | `preparing-for-esg-reporting.html` | Getting started |
| Carbon footprint estimation and ESG: a practical guide | `carbon-footprint-in-construction.html` | Environmental |
| Environmental product declarations: a guide for construction teams | `environmental-product-declarations.html` | Environmental |
| Why traceability actually matters in construction | `traceability-in-construction.html` | Environmental |
| The social side of ESG on construction projects | `social-factors-in-construction.html` | Social |
| Governance in construction: controls that stand up to scrutiny | `governance-in-construction.html` | Governance |
| Blockchain in construction: what it actually does for ESG | `blockchain-in-construction.html` | Technology |

The table follows the reading order used on `blogs.html`. Each article's "Next article" link goes to the following row, and the last row wraps around to the first. The homepage `#blog` section features three articles and links to `blogs.html` for the rest.

### Writing guidelines

- **Evergreen.** No publish dates, no author bylines, and no time-bound phrases such as "next year", "this year" or specific deadlines. Articles show a category and a read time (word count ÷ 200, rounded up) instead of a date.
- **Plain language.** Use sentence-case headings and Canadian spelling (labour, aluminium, program). Avoid marketing filler.
- **Careful claims.**
  - Keep statements about ConstructESG in line with what the homepage says the product does.
  - Laws and standards can be named (for example the GHG Protocol, EN 15978, ISO 14025, or Canada's supply-chain forced-labour reporting law), but don't quote thresholds or dates that will go out of date.
- **Structure.**
  - An intro of one or two paragraphs.
  - `h2` sections.
  - Optionally one pull quote (`blockquote.qoute`).
  - A "Key points" box at the end.
  - Link to related articles in the body text where they come up naturally.

### Adding an article

1. Copy an existing article, such as `governance-in-construction.html`, to `your-slug.html`.
2. Update the `<head>`: `<title>`, `meta description`, `canonical`, and `og:title`, `og:description`, `og:url` and `og:image`. The URLs use `https://constructionesg.ca/`.
3. Replace the `h1`, the category and read time under it, the article body and the Key points.
4. **Cover image.**
   - Add a portrait JPEG with a width-to-height ratio of about 0.7 (324×464, or larger at the same ratio) as `image/jpg/constructesg-your-slug.jpg`.
   - Use it in the sidebar with descriptive `alt` text.
   - To crop an existing photo on macOS: `sips -c <height> <width> in.jpg --out out.jpg`.
5. In the sidebar, point "More to read" at three related articles.
6. Point the new article's "Next article" at the next one in reading order, and point the previous article's "Next article" at the new one.
7. Add a card and a one-sentence excerpt to `blogs.html`. You can also swap it into the three featured cards on `index.html#blog`.
8. Add the new article to the "More to read" lists of one or two related articles.
9. Preview locally at desktop and mobile widths before pushing.

## Known issues and cleanup backlog

**Template leftovers**
- The template pages listed under [Pages](#pages) are still published. `404.html` matters most, because every broken URL shows it.
- `plugins/php/` holds an unused mail script with another company's email addresses.
- `image/home-1` … `home-8`, `image/png/blog-post-*`, `image/png/portfolio-*` and the `image/jpg/portfolio-*` files are template stock images, used only by template pages.

**Homepage content**
- The copy is dated in places: "mandatory ESG reporting … is coming to Canada in 2024" and "reporting requirements you might face next year".
- The phone number in the contact block is a placeholder: `+8 (123) 985 789`.
- The contact block icons are mismatched: an envelope on the address, a phone on the email line, and a map pin on the phone number.
- Several calls to action link to `#` and go nowhere: "Get A Free Audit", "Start Free Trial", the "Ready? Start with a Free Audit" card and the service cards.
- Most homepage images have empty `alt` text.

**Hosting and repository**
- HTTPS isn't enforced in the GitHub Pages settings, so the site still answers on `http://`. Enable "Enforce HTTPS" under Settings → Pages.
- Three `.DS_Store` files are tracked in git.
- `README.md` and `CLAUDE.md` are published on the live domain. To stop that, add a `_config.yml` containing `exclude: [README.md, CLAUDE.md]`.
