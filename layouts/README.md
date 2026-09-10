# Theme layouts

**Status: 95% — structure settled; one custompages conflict to resolve.**

## How layouts work

Each layout registered in `theme.xml` has one file:

```
layouts/<name>_layout.html
```

The file contains the layout's own chrome and `{---}` where the page content
renders. Core inserts the file at the `{---LAYOUT---}` marker of
[theme.html](../skeleton/theme-html.md), **inside** the shared
`<div class="page {PAGE_CLASS}">` wrapper — layout files therefore start at
`.page-wrapper` (or their `aside`), never with `.page` itself.

{% hint style="warning" %}
Every layout declared in `theme.xml` must have its file, and `theme.html` must
exist too — if either is missing, `e_theme::loadLayout()` silently falls back
to legacy `theme.php` layouts.
{% endhint %}

{% hint style="warning" %}
The layout renderer parses **line by line** — keep every shortcode on a single
line in layout files and partials.
{% endhint %}

## Current layouts

| Layout | custompages | Purpose |
|---|---|---|
| [`3columns`](3columns.md) *(default)* | `page faq` | catalogue, content, account — the site's main layout |
| [`full`](full.md) | — | full page, horizontal header, boxed width |
| [`auth`](auth.md) | `login fpw signup` | centered auth pages |
| [`page`](page.md) | `page faq` | boxed page with the magic page header |
| [`sidebar`](sidebar.md) | `news` | magic page header + right menu column |
| [`home`](home.md) | `FRONTPAGE` | full-width marketing front page |
| [`memberdesk`](memberdesk.md) | — | member area, left vertical sidebar |
| [`raw`](raw.md) | — | bare output |

{% hint style="danger" %}
`3columns` and `page` both declare `page faq` in `custompages`. Two layouts
claiming the same pages is unresolved: which one wins depends on the order
core reads them, which is not something to rely on. One of the two entries
needs to go.
{% endhint %}

`layouts/dashboard_layout.html` exists but is **not** registered in
`theme.xml`, so it never renders. It is kept as a prepared variant, not
dead code — but nothing has been verified against it.

## Mobile drawers

Every layout except `auth`, `memberdesk` and `raw` carries three offcanvas
panels, opened from the bottom navigation bar (`{BOTTOM_NAV}`, itself
`d-lg-none`):

| id | Accordion | Menu area |
|---|---|---|
| `#panelLeft` | `#accLeft` | 101 |
| `#panelUser` | `#accUser` | 102 |
| `#panelLatest` | `#accLatest` | 103 |

On `3columns` and `sidebar` these use `offcanvas-lg` — a real column from `lg`
up, a drawer below. Elsewhere they are `offcanvas … d-lg-none`, drawers only.

The block is identical in every layout, and only `3columns` addresses the
areas with plain `{MENUAREA=n}`. Everywhere else it is `{THEME_MENUAREA=n}`,
which mirrors that one layout's assignment — see
[Shared menu areas](../guides/shared-menu-areas.md).

{% hint style="warning" %}
`{BOTTOM_NAV}` renders triggers pointing at those three ids. A layout that
carries the bar but not the panels produces
`Cannot read properties of undefined (reading 'backdrop')` — Bootstrap's
`BaseComponent` returns early on a missing target, leaving `_config` undefined
before `Offcanvas` reads `_config.backdrop`. Add the panels or suppress the bar
in `sc_bottom_nav()`; never one without the other.
{% endhint %}

## Layout contracts in CSS

Three values in `style.css` are shared between the layouts and their JS:

```css
:root {
  --nav-h: 3.5rem;        /* header height; corrected by js/nav-height.js */
  --sticky-gap: 1rem;
  --bottomnav-h: 3.75rem;
}
```

`--nav-h` is written inline on `<body>` by `theme.html` as a pre-measure
default and corrected once the header is rendered — PHP cannot know the final
height, because the navbar wraps at some widths and web fonts change the line
box after load. It feeds `.col-sticky` and the `scroll-margin-top` on anchor
targets, both of which land in the wrong place if it is stale.

The `lg` breakpoint itself cannot be a variable — CSS forbids `var()` inside
`@media` — so it stays a Tabler constant. Changing it means changing
`.offcanvas-lg`, `.d-lg-none` and the media queries together.

## Magic shortcodes — `{---CAPTION---}` and `{---BREADCRUMB---}`

The `page` and `sidebar` layouts render a Tabler `.page-header` whose content
comes from core **magic shortcodes** (`e_render::getMagicShortcodes()`):

* `{---CAPTION---}` — the caption of the page's **main render**,
* `{---BREADCRUMB---}` — the current breadcrumb, by default rendered via
  `e107::getForm()->breadcrumb()`.

Which render is "main" is designated by `{SETSTYLE=default}` — that is why
those two layouts use `default` before `{---}` while the others use `card`.

{% hint style="danger" %}
Never switch the `page` or `sidebar` layout to `{SETSTYLE=card}` — the magic
shortcodes are fed by the `default`-styled main render, and with `card` the
page header stays empty.
{% endhint %}

Core also offers theme hooks: if `theme_shortcodes` defines `sc_caption($caption)`
or `sc_breadcrumb($bread)`, their return value replaces the marker instead of
the default rendering.

## `{SETSTYLE}` styles

Which style each layout uses and what `tablestyle()` renders for it is
documented on [theme.php](../skeleton/theme-php.md). Rule of thumb: `card` for
plain content layouts, `default` only for the layouts with the magic page
header, `card_header` where the block needs a visible caption.

## Per-layout behaviour without per-layout files

* **Body classes** — `{BODY_CLASS}` on the body tag, plus `{LAYOUT_ID}`, which
  expands to `"layout-" + name2id(THEME_LAYOUT)` and gives every layout a
  handle for CSS (see [theme.html](../skeleton/theme-html.md)).
* **Page wrapper classes** — `{PAGE_CLASS}` on the shared `.page` wrapper
  (`auth` → `page-center`).
* **Header/footer variants** — the `{HEADER}` / `{FOOTER}` shortcode switches
  (see [Header & footer partials](header-footer.md)).

{% hint style="danger" %}
In all of these, `THEME_LAYOUT` must be read via `defset()` **at call time,
inside the method**. The theme shortcode batch is a singleton created on the
first `getScBatch('theme')` call anywhere — a plugin or menu can instantiate it
*before* `e_theme::initThemeLayout()` has defined the constant, so constructor
caching works only sometimes.
{% endhint %}
