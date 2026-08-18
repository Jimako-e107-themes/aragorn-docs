# news.php as front page

The news listing can be used as the site's front page. This is tested and
supported in Aragorn.

## Setting it up

**Admin → Preferences → Front Page**, choose the news listing.

The `home` layout then applies to the front page, so the news listing
renders inside `layouts/home_layout.html` rather than the sidebar layout it
uses at `/news.php`.

## How the layout applies

Only the part of the home layout around `{---}` applies — that placeholder
is where the news listing is dropped in. The featurebox sections and any
other markup in the layout render around it as usual.

Because the listing sits inside `{---}`, the `{SETSTYLE=...}` written
immediately before it styles the whole listing, pagination included:

```html
{SETSTYLE=section-white}
{---}
```

Change the style name to move the listing into a different section band.

## Pagination

Pagination belongs to the news listing itself, not to the theme's templates
and not to the news grid. It appears wherever `news.php` renders — so if
pagination shows up on the front page, that is the news listing being the
front page.

It is styled theme-wide in `templates/nextprev_template.php` with Tabler
chevrons, centred.

## When not to use it

For a marketing-style home page with a few recent articles and no
pagination, leave the front page as the standard home page and add a
[News grid](news-grid.md) menu instead. The grid shows a fixed number of
articles with an *All news* button and no pagination.
