# Aragorn Theme

Aragorn is the new flagship theme for [e107sk.com](https://e107sk.com),
built for **e107 core v2.4** on the [Tabler 1.4.0](https://tabler.io) UI kit
(plain Bootstrap 5). It uses the e107 HTML layout system
(`theme.html` + `layouts/*_layout.html`) introduced in e107 v2.2.2.

## What's in this documentation

* **[How this theme was developed](development/README.md)** — a running
  checklist of every development area with its current progress, and one
  page per area recording what was done, which core mechanisms are involved
  and which traps had to be solved (the forgot-password / members-only
  rendering path being the most famous one).
* **[Theme structure](skeleton/README.md)** — one page per file the theme is
  built from: `theme.xml`, `theme.html`, `theme.php`, the shortcode batch,
  the config page and the assets.
* **[Theme layouts](layouts/README.md)** — the eight registered layouts, what
  each is for, and the [header and footer partials](layouts/header-footer.md)
  they share.
* **[Styling](styling/README.md)** — which CSS file a rule belongs in, how
  Tabler's variables actually resolve, how a palette file is built, and the
  Bootstrap 3/4 class names e107 still emits.
* **[Standalone pages](standalone/auth-pages.md)** — the core pages that need
  more than a layout: auth, search, sitedown.
* **[Plugins](plugins/signin.md)** — the template overrides the theme ships
  for core plugins, and why each one exists.
* **[Guides](guides/fixing-news-action-icons.md)** — standalone how-to
  articles distilled from the development notes, written so they apply to
  any custom e107 theme, not only to Aragorn.

## Where to start

Reading front to back is not the intended use. Three entry points cover most
questions:

| If you want to | Start at |
|---|---|
| understand how the theme is put together | [Theme structure](skeleton/README.md) |
| change how something looks | [Styling](styling/README.md) |
| add or change a page's structure | [Theme layouts](layouts/README.md) |

{% hint style="info" %}
If a CSS rule you wrote appears to do nothing,
[Working with Tabler variables](styling/tabler-variables.md) explains the most
common reason before you start bisecting selectors.
{% endhint %}

## Key facts

| | |
|---|---|
| CMS | e107 core v2.4, PHP 8 compatible |
| UI kit | Tabler 1.4.0 (bundles Bootstrap 5 CSS + JS) |
| Layout system | e107 HTML layouts (`theme.html` + `layouts/`) |
| Default layout | `3columns` — catalogue, content, account |
| jQuery | loaded by e107 core itself (always present); head vs. footer position is an e107 config setting. The theme's own code does not use it |
| Icons | Tabler icon webfont, served from the theme; core FontAwesome 6 for legacy shortcode output |
| Palette | one brand file per site, swapped in `theme.php :: css()` |
| License note | Tabler is MIT licensed |
