# News templates

**Status: 60% — main + view templates done; grid, menu and carousel pending.**

## Location

Theme overrides of the news plugin templates:

```
THEME/templates/news/news_template.php        (list / default / category / related)
THEME/templates/news/news_view_template.php   (detail view + schema + nav)
```

{% hint style="warning" %}
A theme override **replaces the plugin template file entirely** — every key
the plugin file defines must exist in the override, including
`['default']['schema']` (structured data). Missing keys break silently.
When updating e107, diff these files against upstream.
{% endhint %}

## What was changed

* **Action buttons** (comment / print / edit) restyled to Tabler
  `.btn-actions` via the `class=` shortcode parameter:
  `{NEWSCOMMENTLINK: class=btn btn-action}` etc. The icons render correctly
  only with the `BOOTSTRAP` and `FONTAWESOME` constants in place — the full
  story is in the
  [news action icons guide](../guides/fixing-news-action-icons.md).
* `{NEWSCOMMENTLINK}` ignores a `glyph=` parameter; its icon comes from
  `['param']['commentlink']` (any HTML, e.g. an inline SVG) or defaults to
  `fa-comment`.
* List style rebuilt from Bootstrap 3 relics (`.thumbnail`, `.span3`) to a
  Tabler `row g-3` layout; related items are Tabler cards.
* BS5 cleanup: `text-justify` removed, `float-md-start`/`me-md-3` wrappers,
  `d-print-none` added alongside legacy `hidden-print`.
* The JSON-LD `schema` block is kept 1:1 with upstream on purpose.

## Remaining work (the missing 40%)

* `news_grid_template.php` — Tabler card grid variants.
* `news_menu_template.php` — list-group / carousel menus. The carousel
  template still carries BS3 `data-ride` / `data-target` attributes that
  Bootstrap 5 ignores, plus another `img-responsive` occurrence — needs a
  real rewrite, not just class swaps.
