# News grid

The news grid is a menu that shows recent articles as a card grid. In
Aragorn it is meant for the home page, as one section among the featurebox
sections — not for `news.php`, which has its own listing.

## Placing it

Two ways, both valid.

### Menu Manager

**Admin → Menus**, drag *News Grid* into a menu area of the home layout.
Its settings — how many items, which category, which layout — are on the
menu's own configuration form.

### Directly in the layout

Write it into `layouts/home_layout.html`:

```html
{MENU: path=news/news_grid&caption=Latest news&limit=6&source=latest&featured=1&layout=col-lg-4}
```

This route is useful when the grid should always be in a fixed position and
you would rather not have it movable from the admin area.

## Parameters

| Parameter | Does |
|---|---|
| `layout` | Which card layout — see the table below |
| `limit` | How many articles |
| `source` | `latest`, `sticky`, or a category |
| `featured` | How many of the first articles get the large card |
| `caption` | Section heading — see below |

## Available layouts

| `layout` value | Result |
|---|---|
| `col-lg-4` | Three cards per row on large screens, two on medium |
| `col-md-4` | Three per row |
| `col-md-3` | Four per row |
| `col-md-6` | Two per row |
| `media-list` | Compact rows with a thumbnail, no cards |

The first article can be given a full-width horizontal card by setting
`featured` to `1`.

## Section heading

The grid only gets Aragorn's section wrapper — the `<section>` band with a
centred heading — when a **caption** is set. Without one, the cards are
output on their own with no wrapper and no heading, and any `{SETSTYLE=...}`
before the menu appears to do nothing.

```html
{SETSTYLE=section}
{MENU: path=news/news_grid&caption=Latest news&limit=6&layout=col-lg-4}
```

There is no in-between: either a caption and the section band, or neither.
For a band with no heading, write the `<section>` markup around the menu in
the layout file yourself.

The same applies to the Menu Manager route — fill in the menu's caption
field.

{% hint style="info" %}
If a grid has no articles to show — an empty category, or `sticky` with no
sticky articles — nothing is output at all, not even an empty band. Sections
that can legitimately be empty will not leave a gap on the page.
{% endhint %}

## Link to all news

Every grid layout ends with a *All news* button linking to the full news
listing, so the grid can sit on a landing page without the article list
behind it. The button's label comes from `LAN_THEME_NEWS_ALL` in the
theme's language file; translate it there.

## Known issue: the Featured setting

{% hint style="danger" %}
The **Featured** setting on the Menu Manager form has no effect — this is a
bug in e107 itself, not in the theme, and it has been reported upstream.
The form saves the value under a different name than the one the news
renderer reads.

Until it is fixed, place the grid directly in the layout instead and write
`featured=1` in the parameters, which works correctly.
{% endhint %}
