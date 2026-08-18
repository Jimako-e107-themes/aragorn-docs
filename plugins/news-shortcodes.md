# News shortcodes

These are the shortcodes Aragorn's news templates use, and the parameters
worth knowing about when editing them. The full list of news shortcodes is
part of e107 itself, not the theme.

## Used in the templates

| Shortcode | Used for |
|---|---|
| `{NEWS_TITLE}` `{NEWS_URL}` | Card and row headings |
| `{NEWS_SUMMARY}` | Teaser text on cards and rows |
| `{NEWS_IMAGE}` | Card images |
| `{NEWS_CATEGORY_NAME}` `{NEWS_CATEGORY_URL}` | The clickable category badge |
| `{NEWS_AUTHOR_AVATAR}` `{NEWS_DATE}` | Card footer |
| `{NEWS_COMMENT_COUNT}` | Comment count badge |
| `{NEWSCOMMENTLINK}` `{NEWSPRINTLINK}` `{NEWSEDITLINK}` | Action buttons in the article view |

## Parameters worth knowing

**Images.** `{NEWS_IMAGE: type=tag&class=card-img-top&placeholder=1}` — the
`type=tag` part returns a bare image so the template can wrap it in its own
link, `placeholder=1` supplies a stand-in when an article has no image, and
`class=` sets the CSS classes. Image size comes from `{SETIMAGE: ...}` at
the top of each template key.

**Text length.** `{NEWS_SUMMARY: limit=100}` truncates the teaser. The
limits differ between layouts — shorter on four-across grids, longer on
horizontal rows.

**Action buttons.** `{NEWSCOMMENTLINK: class=btn btn-action}` — the `class=`
parameter is how these were restyled as Tabler action buttons.
`{NEWSCOMMENTLINK}` ignores a `glyph=` parameter; its icon is set in the
template's `['param']['commentlink']` value instead.

## Two things to avoid

**Do not wrap `{NEWS_SUMMARY}` in `<p>`.** It can output its own paragraph
tags, and a paragraph inside a paragraph is invalid — browsers split it and
the layout breaks. The templates use `<div>` for this reason.

**Do not reintroduce the old shortcode names.** `{NEWSURL}`,
`{NEWSCOMMENTCOUNT}` and `{NEWSMETADIZ}` still work, but each one emits a
deprecation notice on PHP 8, which is visible on the page. Use
`{NEWS_URL}`, `{NEWS_COMMENT_COUNT}` and `{NEWS_SUMMARY}`.

{% hint style="info" %}
`{NEWSMETADIZ}` was not simply renamed. It returns the SEO meta
description, not the summary. Teaser cards in this theme show
`{NEWS_SUMMARY}`, which is the editorial teaser. If you want the meta
description instead, the modern name for it is `{NEWS_DESCRIPTION}`.
{% endhint %}
