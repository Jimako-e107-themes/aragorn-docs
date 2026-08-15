# News: default caption and clickable category badge

Two small but easy-to-miss gaps found while building the news templates,
both traced through `e107_plugins/news/news.php` and
`e107_core/shortcodes/batch/news_shortcodes.php` on e107 core master.

## 1. Missing heading on the plain news listing

**Symptom:** the `page`/`sidebar` layout's `<h2 class="page-title">` (fed by
the `{---CAPTION---}` magic shortcode) is empty on `news.php` with no
query — but works on category/tag listings.

**Cause:** `{---CAPTION---}` is only populated when
`isset($mainRenders[0]['caption'])` is true
(`e_render_class.php::getMagicShortcodes()`). `isset()` returns `false` for
a `null` value. Upstream's own `news_template.php` deliberately sets:

```php
$NEWS_TEMPLATE['default']['caption'] = null; // add a value to use tablerender()
```

— the comment is a direct instruction to theme authors: **supply a real
value**, upstream won't guess one for the unfiltered front listing.

Category and tag listings don't have this problem because they go through
a completely different, older mechanism: `news.php` builds
`$NEWSLISTTITLE` by string-replacing `{NEWSCATEGORY}` inside the `list`
style's `caption` template value, then passes the *already-built string* to
`tablerender()` — never `null`. This theme's `news_template.php` already
had `'{NEWSCATEGORY}'` in the `list` caption, so category/tag pages were
never actually affected by this bug.

**Fix** — give the `default` style a real caption:

```php
$NEWS_TEMPLATE['default']['caption'] = defset('LAN_PLUGIN_NEWS_NAME', 'News');
```

{% hint style="info" %}
For a category or tag listing, the caption already fills in the category
or tag name automatically — no `{NEWS_CATEGORY}` needed there. That
substitution is a plain `{NEWSCATEGORY}` shortcode (no underscore) purely
inside the `list` style's `caption` string, unrelated to the magic
shortcode mechanism above.
{% endhint %}

## 2. Category badge not clickable

**Symptom:** the category badge on listing cards (`{NEWS_CATEGORY_NAME}`)
renders correctly but isn't a link.

**Cause:** not a deliberate SEO choice — an oversight. The plain
`{NEWS_CATEGORY_NAME}` shortcode only returns text:

```php
function sc_news_category_name($parm=null)
{
    if(empty($parm['link']) && isset($this->news_item['category_name']))
    {
        return e107::getParser()->toHTML($this->news_item['category_name'], 'TITLE');
    }
    return $this->sc_newscategory($parm); // link=1 delegates here
}
```

`{NEWS_CATEGORY_NAME: link=1}` *does* produce a link — but
`sc_newscategory()` builds the whole `<a href="...">Name</a>` itself, with
no `class=` parameter (only an inline-style hook via `catlink`). That
makes it unusable for a Tabler `badge` class directly.

**Fix** — use the two single-purpose shortcodes together instead, keeping
full control of the markup:

```html
<a href="{NEWS_CATEGORY_URL}" class="badge text-bg-primary">{NEWS_CATEGORY_NAME}</a>
```

`{NEWS_CATEGORY_URL}` returns just the URL (`null` if the item has no
category — the `href` then simply renders empty, a negligible edge case
since every news item has a category).

Applied everywhere the category badge appears: the `default` style item in
`news_template.php` and the shared card partial in
`news_grid_template.php`. See [News checklist](../plugins/news-checklist.md)
for the live-verification status of this fix.
