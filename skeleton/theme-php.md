# theme.php

Minimal by design: constants, asset registration, `tablestyle()` and one
startup check. No layout markup lives here — that is the HTML layout system's
job.

## Constants

```php
define('BOOTSTRAP', 5);
define('ARAGORN_DRAWER_LAYOUT', '3columns');
```

`ARAGORN_DRAWER_LAYOUT` names the layout whose menu areas feed the mobile
drawers on every other layout. It lives here rather than next to the class
that uses it because `theme.php` is loaded well before `theme_shortcodes.php`,
which is pulled in lazily on the first `getScBatch('theme')` call.

## Asset registration

CSS and JS are registered in `init()` and `css()`; the load order is
documented on [Theme assets](theme-assets.md). No jQuery or Bootstrap
libraries are registered from here — see [theme.xml](theme-xml.md) for why the
`<libraries>` block handles the constants without loading files.

The palette is one line in `css()` and is the only line that changes between
sites:

```php
e107::css('theme', 'css/brand-e107sk.css');
```

See [Styling](../styling/README.md) for what belongs in that file and what
does not.

## `tablestyle()` and the `{SETSTYLE}` map

`tablestyle()` wraps every `tablerender()` output, keyed by mode. Layouts
select the mode with `{SETSTYLE=...}` before their `{---}` / `{MENUAREA}`.

| Style | Used by | Output |
|---|---|---|
| `card` | `full`, `sidebar` aside, `memberdesk` | explicit Tabler card, caption in `.card-header` |
| `card_header` | search, and any block needing a visible caption | Tabler card with a `.card-header` |
| `default` | `page`, `sidebar` content | designates the **main render** for the magic page header; card without caption (the caption lives in the page header) |
| `accordion-left`, `accordion-user`, `accordion-latest` | the three side panels | one accordion item per menu |
| `login_page`, `fpw`, `signup` | auth pages via core | `card card-md`, caption centered inside the body |
| `menu` | menus without explicit style | Tabler card |
| `section*`, `hero` | home layout | full-width section wrappers |
| `raw` / `none` | — | passthrough |

### Mode versus style

`tablestyle($caption, $text, $mode, $data)` receives two different things.
`$data['setStyle']` is what the layout asked for with `{SETSTYLE}`; `$mode` is
what the calling code passed to `tablerender()` and is not under the theme's
control.

A small switch maps a few modes onto a style before the main one runs — this
is how `search_head` and `search_result` reach `card_header` without the
search page knowing anything about the theme.

{% hint style="warning" %}
`$mode` also lands in the card's `class` attribute, so it is sanitised first:

```php
$modeClass = preg_replace('/[^a-z0-9_-]/i', '', (string) $mode);
```

In practice it comes from plugin code rather than from a request, but nothing
guarantees a plugin did not build it from one.
{% endhint %}

### Accordion items

The three `accordion-*` styles delegate to `accordionItem()`, which renders one
Bootstrap accordion item per menu. The enclosing `.accordion` element and its
id live in the layout, not here — `data-bs-parent` is what makes only one item
open at a time, and it needs an id the theme cannot invent per menu.

Items are counted per parent panel so the first item in **each** panel opens,
not just the first on the page. A menu with no caption has nothing to click and
degrades to a plain card rather than producing a headerless accordion item.

## `checkDrawerLayout()`

A startup check, called at the end of `init()`.

e107 stores the default layout's menus with an *empty* `menu_layout` rather
than the layout's name, so `{THEME_MENUAREA}` can only resolve
`ARAGORN_DRAWER_LAYOUT` while that layout is the default one. Changing the
default leaves the drawers reading the wrong rows with nothing on screen to
explain why.

The check warns main administrators when the two disagree. It deliberately does
**not** correct the preference: that would overwrite an administrator's
deliberate choice, and it would mean writing to the database on a front-end
request.

`getperms('0')` rather than `ADMIN`, because only a main administrator can
change the default layout — anyone else would get a message they cannot act on.

See [Shared menu areas](../guides/shared-menu-areas.md) for the fallback that
keeps the drawers working meanwhile.
