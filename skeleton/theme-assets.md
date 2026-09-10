# Theme assets

**Status: 95% — icon webfont now served locally; subsetting still open.**

## What was copied from Tabler

Only the files the theme actually needs, taken from the Tabler 1.4.0
dashboard package (`dist/`) and the `@tabler/icons-webfont` package:

| Source | Theme location | Purpose |
|---|---|---|
| `css/tabler.min.css` | `css/tabler.min.css` | complete UI kit incl. Bootstrap 5 |
| `css/tabler-marketing.min.css` | `css/tabler-marketing.css` | hero/marketing components (home layout) |
| `css/tabler-themes.min.css` | `css/tabler-themes.min.css` | light/dark colour schemes |
| `js/tabler.min.js` | `js/tabler.min.js` | Bootstrap 5 JS + Tabler components |
| `js/tabler-theme.min.js` | `js/tabler-theme.min.js` | light/dark handling, loaded in `<head>` |
| icons-webfont `dist/tabler-icons.min.css` | `css/tabler-icons.min.css` | icon font |
| icons-webfont `dist/fonts/*` | `css/fonts/` | the font files themselves |

Deliberately **not** copied: demo libraries (apexcharts, jsvectormap) and the
extra bundles (flags, payments, socials, vendors). Legacy core output uses the
core FontAwesome 6 library (see [Theme skeleton](README.md)).

## The icon webfont

Icons come from the Tabler icon webfont, served from the theme. A few inline
SVGs remain where they were taken from a Tabler snippet verbatim — the
`{THEME_TOGGLE}` buttons, for one.

{% hint style="danger" %}
Register the stylesheet with `e107::css()`, never with `e107::link()`.

`js_manager::renderLinks()` filters stylesheets out of `e107::link()` and logs
*"ignored. Use e107::css() instead"*. A `<link>` string that omits
`type="text/css"` slips past that filter, so a CDN link written this way
appears to work — and then disappears at the next change, with no error and
nothing in the source to explain it.
{% endhint %}

The `@font-face` rules use relative paths:

```css
url("./fonts/tabler-icons.woff2?v3.46.0")
```

so `css/fonts/` must sit **next to** `css/tabler-icons.min.css`. Move one
without the other and every icon becomes an empty box.

{% hint style="info" %}
The stylesheet is 211 KB for roughly 5900 icons, of which the theme uses a few
dozen. Tabler supports generating a subset. Not urgent, but the first place to
look if page weight becomes a topic.
{% endhint %}

## CSS load order

```
css/tabler-icons.min.css    (icon font)
css/tabler.min.css          (framework)
css/tabler-marketing.css    (home layout components)
css/tabler-themes.min.css   (colour schemes)
[core FontAwesome 6]        (via <libraries>)
css/brand-<site>.css        (palette)
style.css                   (theme overrides - always last)
```

Order is load-bearing, not cosmetic. What may override what, and which file a
given rule belongs in, is on [Styling](../styling/README.md).

## Legacy class conflicts

Core and plugin markup still emits Bootstrap 3 and 4 class names, some of which
Bootstrap 5 or Tabler reused for something else — `img-responsive` being the
one most visible in core templates. They are collected, with what each became
and what would let the patch be deleted, on
[Compatibility shims](../styling/compat-shims.md).

## Social icon SVG sizing

The `aragorn` variant of `{XURL_ICONS}` (see [Sitedown page](../standalone/sitedown.md))
renders core inline SVGs inside Tabler brand buttons. The SVGs carry
hard-coded IcoMoon dimensions, normalized in `style.css`:

```css
.social-links .btn-icon svg {
    width: 1.1rem;
    height: 1.1rem;
    fill: currentColor;
}
```
