# Setup checklist

What to look at on a live site after switching to Aragorn. Everything here
is built and expected to work — this is a verification pass, not a to-do
list. Where something needs explaining, the note links out rather than
explaining it in the table.

## Listings

| # | Check | Expect |
|---|---|---|
| 1 | `news.php` front listing | Teaser card grid, cards equal height |
| 2 | Listing heading | Category name, or the news plugin name on the default listing |
| 3 | Category badge on cards | Visible text, links to the category |
| 4 | `news.php?all` and category listings | Horizontal rows, image left |
| 5 | Tag pages | Heading reads `Tag: "…"` |
| 6 | Pagination | Centred, Tabler chevrons |

## Article view

| # | Check | Expect |
|---|---|---|
| 7 | Action buttons | Comment, print and edit icons visible — [guide](../guides/fixing-news-action-icons.md) |
| 8 | Comments | Rendered by the comment plugin, not yet restyled |
| 9 | Prev/next navigation | Present below the article |

## Menus

| # | Check | Expect |
|---|---|---|
| 10 | Latest news | List rows with count badges |
| 11 | Category menu | Same row style |
| 12 | Archive menu | Expand/collapse works |
| 13 | Carousel menu | Slides advance |
| 14 | Other / Other2 | Carousel and compact rows respectively |
| 15 | Empty menu | Renders nothing at all, leaves no gap |

## News grid

| # | Check | Expect |
|---|---|---|
| 16 | Grid on the home page | Cards in the chosen layout — [News grid](news-grid.md) |
| 17 | Section heading | Only appears when a caption is set — [why](news-grid.md#section-heading) |
| 18 | All news button | Links to the full listing; label from `LAN_THEME_NEWS_ALL` |

## Known issues

| Item | Status |
|---|---|
| **Featured** setting in Menu Manager has no effect | Bug in e107, reported upstream. Use `featured=1` in a layout-placed grid instead — [details](news-grid.md#known-issue-the-featured-setting) |
| Switching the front listing from grid to rows | Under investigation — the `list` setting can still render as a grid on some installs |

## Not restyled

These work but keep their default appearance, so do not report them as
broken:

* The `related` articles block below an article
* Comments inside the article view — these come from the comment plugin
* RSS output
