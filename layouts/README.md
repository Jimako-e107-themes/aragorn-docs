# Theme layouts

**Status: done (100%)** — refinements remain possible; the page-header
markup in particular may later move into `tablestyle()` if a suitable
template mechanism is found.

## How layouts work

Each layout registered in `theme.xml` has one file:

```
layouts/<name>_layout.html
```

The file contains the layout's own chrome and `{---}` where the page
content renders. Core inserts the file at the `{---LAYOUT---}` marker of
[theme.html](../skeleton/theme-html.md), **inside** the shared
`<div class="page {PAGE_CLASS}">` wrapper — layout files therefore start at
`.page-wrapper` (or their `aside`), never with `.page` itself.

{% hint style="warning" %}
Every layout declared in `theme.xml` must have its file, and `theme.html`
must exist too — if either is missing, `e_theme::loadLayout()` silently
falls back to legacy `theme.php` layouts.
{% endhint %}

{% hint style="warning" %}
The layout renderer parses **line by line** — keep every shortcode on a
single line in layout files and partials.
{% endhint %}

## Current layouts

| Layout | custompages | Purpose |
|---|---|---|
| [`full`](full.md) | — | full page, horizontal header, boxed width |
| [`auth`](auth.md) | `login fpw signup` | centered auth pages |
| [`page`](page.md) *(default)* | `page faq` | boxed page with the magic page header |
| [`sidebar`](sidebar.md) | `news` | magic page header + right menu column |
| [`home`](home.md) | `FRONTPAGE` | full-width marketing front page |
| [`memberdesk`](memberdesk.md) | — | member area, left vertical sidebar |
| [`raw`](raw.md) | — | bare output |

Each layout has its own page with the exact structure and rules.

## Magic shortcodes — `{---CAPTION---}` and `{---BREADCRUMB---}`

The `page` and `sidebar` layouts render a Tabler `.page-header` whose
content comes from core **magic shortcodes**
(`e_render::getMagicShortcodes()`):

* `{---CAPTION---}` — the caption of the page's **main render**,
* `{---BREADCRUMB---}` — the current breadcrumb, by default rendered via
  `e107::getForm()->breadcrumb()`.

Which render is "main" is designated by `{SETSTYLE=default}` — that is why
those two layouts use `default` before `{---}` while the others use `card`.

{% hint style="danger" %}
Never switch the `page` or `sidebar` layout to `{SETSTYLE=card}` — the
magic shortcodes are fed by the `default`-styled main render, and with
`card` the page header stays empty.
{% endhint %}

Core also offers theme hooks for customizing the output: if
`theme_shortcodes` defines `sc_caption($caption)` or
`sc_breadcrumb($bread)`, their return value replaces the marker instead of
the default rendering.

## `{SETSTYLE}` styles

Which style each layout uses and what `tablestyle()` renders for it is
documented on [theme.php](../skeleton/theme-php.md). Rule of thumb:
`card` for plain content layouts, `default` only for the layouts with the
magic page header.

## Per-layout behaviour without per-layout files

* **Body classes** — `{BODY_CLASS}` on the body tag
  (see [theme.html](../skeleton/theme-html.md)).
* **Page wrapper classes** — `{PAGE_CLASS}` on the shared `.page` wrapper
  (`auth` → `page-center`).
* **Header/footer variants** — the `{HEADER}` / `{FOOTER}` shortcode
  switches: `auth` and `memberdesk` suppress the header, `memberdesk` loads
  `footers/footer_small.html`
  (see [Header & footer partials](header-footer.md)).

{% hint style="danger" %}
In all three shortcodes, `THEME_LAYOUT` must be read via `defset()` **at
call time, inside the method**. The theme shortcode batch is a singleton
created on the first `getScBatch('theme')` call anywhere — a plugin or menu
can instantiate it *before* `e_theme::initThemeLayout()` has defined the
constant, so constructor caching works only sometimes.
{% endhint %}
