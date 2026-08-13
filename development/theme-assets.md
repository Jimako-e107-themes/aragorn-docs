# Theme assets

**Status: done (100%)**

## What was copied from Tabler

Only the files the theme actually needs, taken from the Tabler 1.4.0
dashboard package (`dist/`):

| Source (Tabler dist) | Theme location | Purpose |
|---|---|---|
| `css/tabler.min.css` | `css/tabler.min.css` | complete UI kit incl. Bootstrap 5 |
| `css/tabler-marketing.min.css` | `css/tabler-marketing.css` | hero/marketing components (home layout) |
| `js/tabler.min.js` | `js/tabler.min.js` | Bootstrap 5 JS + Tabler components |
| `js/tabler-theme.min.js` | `js/tabler-theme.min.js` | light/dark handling, loaded right after `<body>` |

Deliberately **not** copied: demo libraries (apexcharts, jsvectormap) and the
extra bundles (flags, payments, socials, vendors). Tabler icons are inline
SVG, so no icon-font vendor is shipped; legacy core output uses the core
FontAwesome 6 library instead (see [Theme skeleton](theme-skeleton.md)).

## CSS load order

```
css/tabler.min.css          (framework)
css/tabler-marketing.css    (home layout components)
[core FontAwesome 6]        (via <libraries>)
style.css                   (theme overrides - always last)
```

## The `.img-responsive` conflict

e107 core templates still emit the Bootstrap 3 class `img-responsive` on
`<img>` elements (news thumbnails, `{LOGO}` output, ...). Tabler repurposed
that class name as a **background aspect-ratio helper**
(`padding-top: 75%`), which adds an empty band above real images.

Fix in root `style.css` — scoped to `<img>` so Tabler's `div.img-responsive`
helper keeps working:

```css
img.img-responsive {
    padding-top: 0;
    background: none;
}
```

## Social icon SVG sizing

The `aragorn` variant of `{XURL_ICONS}` (see [Sitedown page](sitedown.md))
renders core inline SVGs inside Tabler brand buttons. The SVGs carry
hard-coded IcoMoon dimensions, normalized in `style.css`:

```css
.social-links .btn-icon svg {
    width: 1.1rem;
    height: 1.1rem;
    fill: currentColor;
}
```
