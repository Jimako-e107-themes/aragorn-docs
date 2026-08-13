# theme.html

**Status: done (100%)**

## What it is

`theme.html` is the shared page wrapper of the HTML layout system
(e107 v2.2.2+). It is the only file in the theme that contains the
`<body>` tag. Core loads it in `e_theme::loadLayout()` together with the
current layout file — **both must exist**, otherwise core silently falls
back to legacy `theme.php` layouts.

```html
<body class="{LAYOUT_ID} {BODY_CLASS}" {BODY_ONLOAD}>
<div class="page {PAGE_CLASS}">
{---HEADER---}
{---LAYOUT---}
{---MODAL---}
{---FOOTER---}
</div>
</body>
```

## `{---LAYOUT---}` — the split marker

Core splits `theme.html` at `{---LAYOUT---}` into the internal `_header_`
and `_footer_` parts. Everything above the marker is rendered before the
current layout, everything below after it. The marker itself is replaced by
the content of `layouts/<name>_layout.html` (see
[Theme layouts](../layouts/README.md)).

## The body tag

* **`{LAYOUT_ID}`** — core placeholder, outputs the current layout name as
  a body class (`home`, `page`, ...). Useful for per-layout CSS.
* **`{BODY_CLASS}`** — Aragorn theme shortcode supplying extra per-layout
  body classes (the `home` layout gets Tabler marketing's
  `body-marketing body-gradient` page background). `theme.html` is shared
  by all layouts, so these classes cannot be hard-coded here.

{% hint style="danger" %}
`sc_body_class()` must read `THEME_LAYOUT` via `defset()` **inside the
method**, never cache it in the constructor — the shortcode batch is a
singleton that can be instantiated before the constant exists. The full
explanation is in [Theme layouts](../layouts/README.md).
{% endhint %}

* **`{BODY_ONLOAD}`** — core placeholder for legacy onload attributes.

## The `.page` wrapper

Tabler's structural root `.page` lives here — not in the layout files —
because the `{---HEADER---}` partial must render **inside** it
(Tabler structure: `.page` > header > `.page-wrapper`). The structural
class `page` is hard-coded; per-layout modifiers come from the
**`{PAGE_CLASS}`** theme shortcode (the `auth` layout returns
`page-center`, producing `.page.page-center` for the centered auth pages).
Keeping `page` hard-coded is deliberate defence: if the shortcode ever
returns nothing, the page loses a modifier, not its skeleton.

## Other markers and shortcodes in theme.html

* **`{---HEADER---}` / `{---FOOTER---}`** — replaced by core with the
  parsed output of the `{HEADER}` / `{FOOTER}` theme shortcodes, which load
  the HTML partials from `headers/` and `footers/`. Documented on
  [Header & footer partials](../layouts/header-footer.md) and in detail in
  [the guide](../guides/header-footer-partials.md).
* **`{---MODAL---}`** — core placeholder for the modal container markup.

## When theme.html is NOT used

Two core render paths bypass the whole HTML layout system — expect neither
`theme.html` nor any layout file to apply there:

1. **`e_IFRAME` mode** — `login.php` by default, and `fpw.php` /
   `signup.php` when *Members only* is enabled. Core discards the theme's
   header/footer and prints `e_IFRAME_HEADER` / `e_IFRAME_FOOTER` from the
   page templates instead; with an HTML-layout theme the page then has no
   `<body>` tag at all. How the auth pages deal with this is documented on
   [Auth pages](../standalone/auth-pages.md).
2. **Maintenance mode** — guests are redirected to `sitedown.php`, whose
   template supplies its **own complete HTML document** including all CSS
   links. See [Sitedown page](../standalone/sitedown.md).
