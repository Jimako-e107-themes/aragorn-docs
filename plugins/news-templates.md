# News templates

## Files

Aragorn overrides four of the news plugin's template files:

| File | Covers |
|---|---|
| `templates/news/news_template.php` | Front listing, category listing, related items |
| `templates/news/news_view_template.php` | Article view, structured data, prev/next |
| `templates/news/news_grid_template.php` | Grid variants — see [News grid](news-grid.md) |
| `templates/news/news_menu_template.php` | Latest, category, months, other, carousel, archive menus |

All four sit in the theme folder and load automatically. No admin setting
points at them.

{% hint style="warning" %}
A theme override replaces the plugin's template file completely — the two
are not merged. If a future e107 release adds a new key to one of these
files, the theme will not have it and that part of the output will fall
back or break silently. Compare these four files against the plugin
versions after every e107 upgrade.
{% endhint %}

## Which pattern each listing uses

| Template key | Where it shows | Tabler pattern |
|---|---|---|
| `default` | `news.php` front listing | [Gallery](https://preview.tabler.io/gallery.html) teaser cards — image, category badge, title, summary, author, date, comment count |
| `list` | `news.php?all`, category listings | [Job listing](https://preview.tabler.io/job-listing.html) horizontal rows — image left, text right |
| `related` | Below an article | Inherited from the plugin, not restyled |

The full article body no longer appears on the front listing — only the
summary. The body shows in the article view.

Which of `default` and `list` is used on the front listing is set in
**Admin → News → Preferences**.

## News menus

The latest, category, months and other2 menus all use the same Tabler
pattern: `list-group list-group-flush list-group-hoverable` rows with a
count badge on the right. The archive menu keeps its expand/collapse
behaviour from the plugin. The carousel menu and the `other` menu are
Bootstrap 5 carousels.

## What changed from the plugin defaults

* **Bootstrap 5 cleanup.** `text-justify`, `.thumbnail`, `.span3`,
  `pull-left` / `pull-right`, `img-responsive` and the old carousel
  attributes (`data-ride`, `data-target`, `data-slide-to`) were all
  replaced with their Bootstrap 5 equivalents.
* **Equal-height cards.** Every card grid uses `row-deck row-cards`, so
  cards in a row match height regardless of how long the summary is.
* **Action buttons** (comment, print, edit) are Tabler `.btn-action`
  buttons. Their icons depend on the theme's FontAwesome registration —
  see [Fixing broken action icons](../guides/fixing-news-action-icons.md)
  if they come out blank.
* **Count badges** use `text-bg-*` rather than `bg-*`, otherwise the text
  is invisible — see
  [Fixing invisible badge text](../guides/fixing-badge-text-color.md).
* **Clickable category badge** on teaser cards, which the plugin's own
  template does not link — see
  [Default caption and clickable category badge](../guides/news-caption-and-category-link.md).
* **Structured data** (the JSON-LD block in the article view) is kept
  exactly as the plugin ships it, so search engines see no change.
