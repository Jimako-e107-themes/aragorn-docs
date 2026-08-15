# News templates

**Status: done (100%)**

## Location

Theme overrides of the news plugin templates:

```
THEME/templates/news/news_template.php        (list / default / category / related)
THEME/templates/news/news_view_template.php   (detail view + schema + nav)
THEME/templates/news/news_grid_template.php    (grid variants: col-md-*, col-lg-4, media-list)
THEME/templates/news/news_menu_template.php    (category / months / latest / other / carousel / archive)
```

{% hint style="warning" %}
A theme override **replaces the plugin template file entirely** — every key
the plugin file defines must exist in the override, including
`['default']['schema']` (structured data). Missing keys break silently.
When updating e107, diff these files against upstream.
{% endhint %}

## Patterns used

* **`default` (news.php front listing)** — Tabler
  [gallery.html](https://preview.tabler.io/gallery.html) teaser card grid:
  image, category badge, title, summary, author avatar + date, comment
  count. The full article body no longer shows on this listing — only in
  the news view.
* **`list` (news.php?all / category listing)** — Tabler
  [job-listing.html](https://preview.tabler.io/job-listing.html) horizontal
  card rows (image left, content right).
* **Grid variants** (`col-md-6/4/3`, `col-lg-4`, `media-list`) — same
  gallery/job-listing card patterns, column class only differs between
  variants; the featured item is a full-width horizontal card.
* **Menu templates** (category, months, latest, other2) — Tabler
  `list-group list-group-flush list-group-hoverable` rows with count
  badges, replacing Bootstrap 3 media-object markup.
* Every card grid uses `row-deck row-cards` so cards in the same row share
  equal height regardless of text length.

## What was changed from upstream

* **Action buttons** (comment / print / edit) restyled to Tabler
  `.btn-actions` via the `class=` shortcode parameter:
  `{NEWSCOMMENTLINK: class=btn btn-action}` etc. The icons render correctly
  only with the `BOOTSTRAP` and `FONTAWESOME` constants in place — the full
  story is in the
  [news action icons guide](../guides/fixing-news-action-icons.md).
* `{NEWSCOMMENTLINK}` ignores a `glyph=` parameter; its icon comes from
  `['param']['commentlink']` (any HTML, e.g. an inline SVG) or defaults to
  `fa-comment`.
* BS5 cleanup throughout: `text-justify`, `.thumbnail`, `.span3`,
  `pull-right`/`pull-left`, `img-responsive` and BS3 carousel data
  attributes (`data-ride`, `data-target`, `data-slide-to`) replaced with
  their `data-bs-*` / utility-class equivalents.
* Count badges (`badge bg-primary`) changed to `badge text-bg-primary` —
  see [Fixing invisible badge text](../guides/fixing-badge-text-color.md)
  for why the plain `bg-*` class isn't enough.
* The JSON-LD `schema` block is kept 1:1 with upstream on purpose.

## Pagination

News listing pagination is core's `{NEXTPREV}`, restyled theme-wide (not
just for news) in `templates/nextprev_template.php` — Tabler chevron icons,
centered. See [theme.php](../skeleton/theme-php.md) for where it's
registered.
