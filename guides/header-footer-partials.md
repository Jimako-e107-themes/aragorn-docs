# Header & footer partials with working menu areas

This page documents a theme pattern used by Aragorn that is **not an
official e107 standard**: page header and footer live in plain HTML partial
files (`headers/*.html`, `footers/*.html`) loaded through theme shortcodes,
and — the important part — `{MENU}` / `{MENUAREA}` shortcodes inside those
partials are fully functional: they render on the front-end **and** their
areas appear in the Menu Manager, including the *Add* dropdown.

That last sentence resolves a problem first reported in 2019
([e107inc/e107#3987](https://github.com/e107inc/e107/issues/3987), later
[discussion #5226](https://github.com/e107inc/e107/discussions/5226)):
menu areas emitted from theme shortcodes were invisible to the Menu Manager
and unparsed on the front-end. Both issues were closed in 2026 after this
pattern proved they no longer occur with the HTML layout system.

## What core provides vs. what the theme adds

**Core** (v2.2.2+ HTML layout system): `theme.html` may contain the markers
`{---HEADER---}` and `{---FOOTER---}`. During `e_theme::loadLayout()` core
replaces each marker with the parsed output of a theme shortcode of the
same name:

```php
// e107_handlers/theme_handler.php (loadLayout)
$LAYOUT['_header_'] = str_replace('{---HEADER---}', $tp->parseTemplate('{HEADER}'), $LAYOUT['_header_']);
$LAYOUT['_footer_'] = str_replace('{---FOOTER---}', $tp->parseTemplate('{FOOTER}'), $LAYOUT['_footer_']);
```

Core does **not** define what `{HEADER}` / `{FOOTER}` return — that is the
theme's job. If the shortcodes don't exist, the markers simply produce
nothing.

**The theme adds** the convention: the shortcodes load partial files from
the theme directory, with a prepared switch for per-layout variants.

## Why this makes menu areas work

The replacement above happens **inside `loadLayout()`** — i.e. *before* the
layout string is parsed and *before* the Menu Manager scans it. The partial
content therefore becomes an ordinary part of the layout, and every
consumer sees it:

1. **Front-end** — `renderLayout()` parses the merged string;
   `{MENUAREA=101}` inside the partial renders through the core single
   shortcode like anywhere else.
2. **Menu Manager preview** — the preview iframe scans the merged
   `$HEADER.$FOOTER` string; the area shows up as a drop zone.
3. **Menu Manager "Add" dropdown** — `menumanager_class::getLayouts()`
   builds per-layout templates as `_header_ + layout + _footer_` and counts
   areas with a regex accepting `{MENU|MENUAREA=NNN}` (1–3 digit id); the
   area appears as an *Area NNN* button.

Under the legacy system the footer emitted by a shortcode existed only at
render time: it was never part of the scanned strings (problems 2 and 3)
and its output was never re-parsed (problem 1). That was the whole
2019 bug.

## Implementation

### 1. `theme.html`

```html
<body class="{LAYOUT_ID} {BODY_CLASS}" {BODY_ONLOAD}>
{---HEADER---}
{---LAYOUT---}
{---MODAL---}
{---FOOTER---}
</body>
```

### 2. Theme shortcodes

```php
// {FOOTER} - loads footers/footer_<variant>.html
function sc_footer()
{
    switch(defset('THEME_LAYOUT'))
    {
        case 'error':
            return '';

        case 'memberdesk':
            $footerName = 'footer_small.html';
            break;

        default:
            $footerName = 'footer_default.html';
            break;
    }

    // __DIR__ on purpose - see "Path resolution" below.
    $footerPath = __DIR__.'/footers/'.$footerName;

    return file_exists($footerPath) ? file_get_contents($footerPath) : '';
}
```

`sc_header()` is identical with `headers/header_<variant>.html`. The switch
is the prepared hook for per-layout variants; until they exist, every
layout loads the default partial.

### 3. Partial files

```
headers/header_default.html
footers/footer_default.html
```

Plain HTML with shortcodes — `{MENUAREA=101}`, `{NAVIGATION=footer}`,
`{XURL_ICONS}`, `{SITELOGO: h=70}`, ... No PHP, no escaping, directly
diffable.

## The rules (each one paid for in debugging time)

### Path resolution — always `__DIR__`

The partial path must be built with `__DIR__`, never with the `THEME`
constant or the `sitetheme` preference:

* `THEME` points to the **admin theme** inside the admin area — and the
  Menu Manager builds its *Add* list in admin context. With `THEME` the
  partial is not found there, `{FOOTER}` returns an empty string, and every
  menu area inside it silently vanishes from the *Add* dropdown (the exact
  symptom of the historic bug, resurrected).
* `e107::getPref('sitetheme')` ignores a user-selected theme
  (`USERTHEME`).
* `USERTHEME` itself is forced off while the Menu Manager is active.

`__DIR__` resolves to the directory of `theme_shortcodes.php` itself —
whichever theme's shortcode batch is executing, its own partials sit next
to it. Correct in every context, immune to future core changes.

### `THEME_LAYOUT` — read at call time

Read the constant with `defset('THEME_LAYOUT')` **inside the shortcode
method**. The shortcode batch is a singleton created on the first
`getScBatch('theme')` call anywhere; caching the constant in the
constructor works only sometimes. Details in
[Theme layouts](../development/theme-layouts.md).

### One shortcode per line

The layout renderer parses **line by line**. A shortcode wrapped across
lines is not parsed. Keep `{MENUAREA=101}` and friends each on a single
line in the partials.

### No HTML comments in partials

The Menu Manager preview prints `<!-- ... -->` content as visible text.
Keep partials comment-free; document the file in the loading shortcode's
docblock instead.

### Menu area ids

The Menu Manager regex accepts 1–3 digit ids (`{MENUAREA=1}` …
`{MENUAREA=999}`). Pick a dedicated range for partial areas (Aragorn uses
101+) so they don't collide with layout-body areas.

## Verification checklist

After adding a menu area to a partial:

1. Front-end: the area's assigned menus render in the header/footer.
2. Admin → Menu Manager: the area shows as a drop zone in the preview.
3. Menu Manager → select an inactive menu → *Add*: the *Area NNN* button is
   listed for every layout.

If (2) works but (3) doesn't, the `{HEADER}`/`{FOOTER}` shortcode returned
nothing in **admin** context — in practice always a path built from the
wrong constant. See "Path resolution" above.
