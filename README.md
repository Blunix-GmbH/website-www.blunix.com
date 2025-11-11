# Blunix.com Website (Hugo)

This repository contains the source for the [Blunix GmbH](https://www.blunix.com) marketing site built with [Hugo](https://gohugo.io/). The site uses the custom `hugo-theme-blunix` theme (managed as a git submodule) and features a flexible block-based page layout system with multilingual support (English and German).

## Prerequisites

- **Hugo Extended** (v0.120.0 or later) — [Installation guide](https://gohugo.io/installation/)
- **Git** — For submodule management

## Quick Start

### Clone the Repository

When cloning this site, you need to initialize the theme submodule:

```bash
git clone --recurse-submodules git@github.com:Blunix-GmbH/website-www.blunix.com.git
cd website-www.blunix.com
```

Or if you've already cloned without submodules:

```bash
git clone git@github.com:Blunix-GmbH/website-www.blunix.com.git
cd website-www.blunix.com
git submodule update --init --recursive
```

### Run Locally

```bash
hugo server
```

Then open http://localhost:1313/ in your browser.

### Build for Production

```bash
hugo --minify
```

Output goes to `public/` directory.

## Site Structure

```
website-www.blunix.com/
  config/
    _default/
      config.toml           # Main configuration
      languages.toml        # Language settings (EN, DE)
      menus.*.toml          # Navigation menus per language
      params.toml           # Site parameters
  content/
    en/                     # English content
      _index.md             # Homepage
      blog/                 # Blog posts
      *.md                  # Service pages
    de/                     # German content
      _index.md
      blog/
      *.md
  themes/
    hugo-theme-blunix/      # Theme (git submodule)
  static/                   # Site-specific static files (if any)
  public/                   # Generated site (gitignored)
```

## Editing Content

All content lives in the `content/` directory, organized by language:

- `content/en/` — English pages
- `content/de/` — German pages

Each page is a Markdown file with front matter. The theme uses a **block-based layout system** where you compose pages from reusable blocks defined in the front matter.

### Example Page Structure

```yaml
---
title: "Linux Consulting Services"
description: "Professional Linux consulting for your business"
image: "/images/services/consulting.webp"

blocks:
  - block: hero-breadcrumb
    title: "Linux Consulting"
    subtitle: "Expert Solutions"
    background: "/images/hero.webp"

  - block: text-image
    title: "Why Choose Us"
    text: "We provide comprehensive Linux consulting..."
    image:
      src: "/images/team.webp"
      alt: "Our team"

  - block: cta
    title: "Ready to Get Started?"
    button:
      text: "Contact Us"
      link: "/contact/"
---
Additional content goes here (optional).
```

### Available Blocks

The theme provides 28+ reusable blocks including:

- **Hero sections**: `hero`, `hero-breadcrumb`, `banner`
- **Content blocks**: `text-image`, `text-image-bg`, `about`, `principles`
- **Features**: `features-grid`, `features-list`
- **Pricing**: `pricing-tabs`, `pricing-2`
- **Interactive**: `faq`, `contact-standard`, `contact-e2ee`
- **Social proof**: `blog-cards`, `examples-slider`, `partners-scroller`
- **Specialized**: `cta`, `emergency-section`, `process-timeline`, `technologies-columns`

See the [theme README](themes/hugo-theme-blunix/README.md) for detailed block documentation.

## Configuration

### Site Settings

Main configuration is in `config/_default/config.toml`:

```toml
baseURL = 'https://www.blunix.com/'
title = 'Blunix GmbH'
theme = "hugo-theme-blunix"
defaultContentLanguage = "en"
```

### Site Parameters

Customize branding and contact info in `config/_default/params.toml`:

```toml
logo = '/images/logo.svg'
footer_logo = '/images/logo-full.svg'

[[contact]]
name = "info@blunix.com"
icon = "fa-solid fa-envelope"
link = "mailto:info@blunix.com"

[[contact]]
name = "+49 30 / 629 322 67"
icon = "fa-solid fa-phone"
link = "tel:+493062932267"
```

### Navigation Menus

Menus are configured per language:

- `config/_default/menus.en.toml` — English navigation
- `config/_default/menus.de.toml` — German navigation

### Languages

Language configuration in `config/_default/languages.toml`:

```toml
[en]
languageName = "English"
contentDir = "content/en"
weight = 1

[de]
languageName = "Deutsch"
contentDir = "content/de"
weight = 2
```

## Theme Management

The site uses the `hugo-theme-blunix` theme as a git submodule pointing to:
`git@github.com:Blunix-GmbH/hugo-theme-blunix.git`

### Update Theme to Latest Version

```bash
git submodule update --remote --merge themes/hugo-theme-blunix
git add themes/hugo-theme-blunix
git commit -m "Update theme to latest version"
```

### Check Current Theme Version

```bash
cd themes/hugo-theme-blunix
git log -1 --oneline
cd ../..
```

### Make Changes to the Theme

If you need to modify the theme:

1. Make changes in `themes/hugo-theme-blunix/`
2. Commit and push to the theme repo:
   ```bash
   cd themes/hugo-theme-blunix
   git add .
   git commit -m "Update theme: description of changes"
   git push
   cd ../..
   ```
3. Update the site to reference the new theme commit:
   ```bash
   git add themes/hugo-theme-blunix
   git commit -m "Update theme reference"
   git push
   ```

### Theme Documentation

For detailed theme documentation, see:
[themes/hugo-theme-blunix/README.md](themes/hugo-theme-blunix/README.md)

## Deployment

### Netlify

The site is configured for Netlify deployment via `netlify.toml`:

```toml
[build]
  command = "git submodule update --init --recursive && hugo --gc --minify"
  publish = "public"

[build.environment]
  HUGO_VERSION = "0.152.2"
```

Netlify automatically:

1. Initializes the theme submodule
2. Builds with Hugo
3. Publishes from `public/`

### Manual Deployment

For other hosting providers:

1. Initialize submodules: `git submodule update --init --recursive`
2. Build: `hugo --minify`
3. Deploy the `public/` directory

## Development Workflow

### Local Development

```bash
hugo server
```

The site will be available at http://localhost:1313/ with live reload.

### View Draft Content

```bash
hugo server -D
```

### Build for Production

```bash
hugo --minify
```

### Clean Build

```bash
hugo --gc --minify
```

## Blog Posts

Blog posts are organized in `content/{lang}/blog/`:

- `content/en/blog/*.md` — English posts
- `content/de/blog/*.md` — German posts

### Create a New Blog Post

```bash
hugo new content/en/blog/my-post-title.md
hugo new content/de/blog/my-post-title.md
```

Edit the front matter and add your content.

## Customization

### Override Theme Templates

To customize a theme template without modifying the theme itself, copy it to your site's `layouts/` directory with the same path:

```bash
mkdir -p layouts/partials
cp themes/hugo-theme-blunix/layouts/partials/footer.html layouts/partials/footer.html
# Edit layouts/partials/footer.html
```

Your version will take precedence over the theme's.

### Add Site-Specific Static Files

Place site-specific static files in the `static/` directory at the site root:

```
static/
  images/          # Site-specific images
  files/           # Downloads (PDFs, etc.)
  robots.txt       # Site overrides
```

These will be merged with theme static files during build.

## Multilingual Content

The site supports English and German:

### Adding Translations

1. Create content in both languages:

   ```
   content/en/my-page.md
   content/de/my-page.md
   ```

2. Ensure the URL slug matches (or use `url:` in front matter)

3. Use the same front matter structure in both files

### Translation Strings

UI translations are defined in the theme's `i18n/` files:

- `themes/hugo-theme-blunix/i18n/en.yaml`
- `themes/hugo-theme-blunix/i18n/de.yaml`

To override or add translations, create your own `i18n/` directory at the site root.

## Support

For issues or questions:

- **Theme issues**: See [hugo-theme-blunix repository](https://github.com/Blunix-GmbH/hugo-theme-blunix)
- **Site content**: Contact Blunix GmbH

## License

Content and configuration: Proprietary — Blunix GmbH
Theme: MIT License — See theme LICENSE file

---

Built with ❤️ by [Blunix GmbH](https://www.blunix.com)
