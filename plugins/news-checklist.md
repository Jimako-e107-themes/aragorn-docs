# News checklist

The templates are written and pass static checks (tag balance, quote
balance, core key coverage), but **"written" is not "verified"**. This page
tracks what still needs a live look, one row per concern, so nothing gets
silently assumed done. Update the status column as items get checked on
the live site; each `Fixed` item that required a real code/template change
links to the guide documenting why.

| # | Item | Status | Notes |
|---|---|---|---|
| 1 | Default listing caption (`news.php`, no query) | Fixed, needs live check | `caption` was `null` → `{---CAPTION---}` stayed empty (`isset()` is false for `null`). Now set to `LAN_PLUGIN_NEWS_NAME`. See [guide](../guides/news-caption-and-category-link.md). |
| 2 | Category listing caption | Should already work, needs live check | `list` style caption already contains `{NEWSCATEGORY}`, substituted by core before `tablerender()` — independent of the magic-shortcode mechanism in #1. |
| 3 | Tag page caption | Should already work, needs live check | Core sets `$category_name = 'Tag: "..."'` for tag searches, feeding the same `{NEWSCATEGORY}` substitution as category listings. |
| 4 | Category badge not clickable | Fixed, needs live check | Was plain `{NEWS_CATEGORY_NAME}` (no link). `{NEWS_CATEGORY_NAME: link=1}` exists but returns its own unstyled `<a>` with no `class=` hook — used `{NEWS_CATEGORY_URL}` + `{NEWS_CATEGORY_NAME}` combined instead for class control. See [guide](../guides/news-caption-and-category-link.md). Not an SEO choice — an oversight. |
| 5 | List ⇄ Default display switch | **Open — needs live HTML** | Admin pref `news_default_template` should pick `$NEWS_TEMPLATE['list']` (full-width job-listing rows) vs `['default']` (teaser grid). Reported symptom: switching to `list` still shows a cramped 2-column grid. Core code traced does select the right array key — root cause not identified without seeing the actual rendered HTML/classes. Please paste a `view-source` snippet when you can reproduce it. |
| 6 | Action icons (comment/print/edit) | Done | See the [action icons guide](../guides/fixing-news-action-icons.md) (`BOOTSTRAP`/`FONTAWESOME` constants). Icons are FontAwesome glyphs, not Tabler SVG — acceptable per that guide, revisit only if visual consistency becomes a priority. |
| 7 | Badge text contrast (`bg-*` → `text-bg-*`) | Done | See [guide](../guides/fixing-badge-text-color.md). |
| 8 | Latest news menu | Built, needs live check | `list-group-hoverable` rows with count badge (fix #7 applies). |
| 9 | Category menu | Built, needs live check | Same pattern as #8. |
| 10 | Months menu (deprecated upstream) | Built, needs live check | Kept for admin-selection compatibility only; archive menu is the recommended replacement. |
| 11 | Other / Other2 menus | Built, needs live check | `other` = single-item carousel; `other2` = compact `list-group` rows (rebuilt from BS3 media-object markup). |
| 12 | Archive menu | Built, needs live check | `e-expandit` JS behaviour kept from core; classes updated to BS5 only. |
| 13 | Carousel menu | Built, needs live check | BS3 `data-ride`/`data-target`/`data-slide-to` replaced with `data-bs-*`; `img-responsive` replaced with `img-fluid w-100`. Needs a live check that Bootstrap's JS carousel still initializes correctly with the new attributes. |
| 14 | Grid menu on the home page | Documented pattern, not yet wired in | Two ways to place a news grid on `home_layout.html`: inline `{MENU: path=news/news_grid&limit=3&layout=col-lg-4}` (no caption) or Menu Manager area with `{SETSTYLE=section}{MENU=102}` (caption via the new `section` tablestyle). See [Home layout](../layouts/home.md) once that page is written up. |
| 15 | Pagination centering | Done | Theme-wide fix in `nextprev_template.php`, not news-specific. |
| 16 | Equal-height cards (`row-deck`) | Done | Applied to every card grid start in `news_template.php` / `news_grid_template.php`. |

## Legend

* **Done** — verified working, or a static/structural fix with no live
  ambiguity left (e.g. a class rename).
* **Fixed, needs live check** — a concrete bug with a known cause and an
  applied fix, not yet confirmed on the live site.
* **Should already work, needs live check** — traced through core logic to
  a conclusion, but never an observed symptom to confirm against.
* **Built, needs live check** — Tabler markup written and passes static
  checks, but never actually rendered/tested.
* **Open** — cause not yet identified; needs more information (usually
  rendered HTML) before a fix can be attempted.
