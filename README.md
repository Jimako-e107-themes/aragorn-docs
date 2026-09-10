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
* **[Guides](guides/fixing-news-action-icons.md)** — standalone how-to
  articles distilled from the development notes, written so they apply to
  any custom e107 theme, not only to Aragorn.

## Key facts

| | |
|---|---|
| CMS | e107 core v2.4, PHP 8 compatible |
| UI kit | Tabler 1.4.0 (bundles Bootstrap 5 CSS + JS) |
| Layout system | e107 HTML layouts (`theme.html` + `layouts/`) |
| jQuery | loaded by e107 core itself (always present); head vs. footer position is an e107 config setting. The theme's own code does not use it |
| Icons | Tabler inline SVG + core FontAwesome 6 for legacy shortcode output |
| License note | Tabler is MIT licensed |
