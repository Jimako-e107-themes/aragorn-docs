# Header & footer partials

**Status: 85% — mechanism done and verified; footer content being finished.**

## What they are

Page header and footer live in plain HTML partial files:

```
headers/header_default.html
footers/footer_default.html
```

They are loaded by the `{HEADER}` / `{FOOTER}` theme shortcodes, which core
merges into the layout at the `{---HEADER---}` / `{---FOOTER---}` markers
of [theme.html](theme-html.md) **during** `e_theme::loadLayout()` — before
the layout is parsed and before the Menu Manager scans it.

This pattern is **not an e107 standard** (core only provides the markers
and the shortcode expansion; the partial-file convention is Aragorn's) and
it is what finally made `{MENUAREA}` work in the footer — a problem
reported upstream in 2019
([#3987](https://github.com/e107inc/e107/issues/3987)) and closed in 2026.

{% hint style="info" %}
The complete pattern — core vs. theme responsibilities, implementation,
and the five hard-earned rules (`__DIR__` paths, `defset()` timing, one
shortcode per line, no HTML comments in partials, menu-area id ranges) —
is documented as a standalone guide:
[Header & footer partials with working menu areas](../guides/header-footer-partials.md).
{% endhint %}

## Current state

* **Header** (`header_default.html`) — Tabler horizontal navbar with the
  site brand, `{NAVIGATION}` menu and the signin area
  (see [Signin](signin.md) for its own status).
* **Footer** (`footer_default.html`) — two-tier Tabler footer:
  `{MENUAREA=101}` (verified live in the Menu Manager, including the *Add*
  dropdown), resources columns, `{NAVIGATION=alt}` / `{NAVIGATION=alt4}`,
  `{SITELOGO}`, `{CONTACT_INFO}`, bare `{XURL_ICONS}` (networks managed in
  e107 prefs, Tabler brand-button markup from the theme's social template
  override), bottom bar with `{NAVIGATION=footer}` and copyright.
* Per-layout switching is live: the `auth` and `memberdesk` layouts
  suppress the header partial entirely (auth is a centered page without a
  navbar; memberdesk replaces the horizontal header with its vertical
  sidebar), `auth` also suppresses the footer, and `memberdesk` loads the
  reduced `footers/footer_small.html`. Further variants
  (`header_home.html`, ...) remain prepared as commented switch cases.

## Remaining work (the missing 15%)

* Footer content finalization — decide which columns stay hardcoded and
  which move to menu areas; final spacing pass.
* Header polish follows the [Signin](signin.md) and navigation work.
