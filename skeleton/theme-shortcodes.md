# theme_shortcodes.php

Custom shortcodes live in `theme_shortcodes.php`, a class extending
`e_shortcode`. They are available in all theme layouts and templates parsed by
the full parser.

Each shortcode has its own page. This one holds what applies to all of them.

## The shortcodes

| Shortcode | Purpose | Status |
|---|---|---|
| [`{HEADER}`](shortcodes/header.md) | loads `headers/header_<variant>.html` | done |
| [`{FOOTER}`](shortcodes/footer.md) | loads `footers/footer_<variant>.html` | done |
| [`{BODY_CLASS}`](shortcodes/body-class.md) | per-layout `<body>` classes | done |
| [`{PAGE_CLASS}`](shortcodes/page-class.md) | per-layout classes for the `.page` wrapper | done |
| [`{THEME_TOGGLE}`](shortcodes/theme-toggle.md) | dark/light switch | done |
| [`{THEME_MENUAREA}`](shortcodes/theme-menuarea.md) | one layout's menu area, rendered on all of them | done |
| [`{BOTTOM_NAV}`](shortcodes/bottom-nav.md) | mobile bottom bar, opens the drawers | 85% |
| [`{TILES}`](shortcodes/tiles.md) | shortcut tiles under the header | 40% |
| `{ADVANCED_LOGIN_LINK}` | styled link to the login page | done |
| `{ADVANCED_SIGNUP_LINK}` | styled link to the signup page | done |
| `{ADVANCED_FPW_BUTTON}` | styled forgot-password button | done |
| `{THEME_PREF}` | reads a theme preference into a layout | done |
| `{SITELOGO}`, `{LOGO}` | site logo, with `type=url` support | done |

Pages for the last five are still to be written.

{% hint style="info" %}
A page without a code snippet means the shortcode is still moving — the item
list, the suppression rules or the markup are unfinished, and a snippet would
go stale before anyone read it. The page says so explicitly and lists what is
open.
{% endhint %}

## Reading `THEME_LAYOUT`

{% hint style="danger" %}
In every shortcode that branches on the layout, read `THEME_LAYOUT` via
`defset()` **at call time, inside the method**.

The batch is a singleton created on the first `getScBatch('theme')` call
anywhere on the page, and a plugin or a menu can instantiate it *before*
`e_theme::initThemeLayout()` has defined the constant. Caching it in the
constructor works only sometimes, which is worse than never working — it fails
on some pages and not others, with nothing to distinguish them.
{% endhint %}

Affected: `{HEADER}`, `{FOOTER}`, `{BODY_CLASS}`, `{PAGE_CLASS}`,
`{BOTTOM_NAV}`, `{TILES}`.

## Paths inside a shortcode

{% hint style="danger" %}
Use `__DIR__`, not `THEME`, for a file path inside the theme.

In an admin context — Menu Manager's preview is the one that will catch you —
`THEME` points at the **admin** theme. `__DIR__` is the directory of
`theme_shortcodes.php` and is right in every context.
{% endhint %}

## Where theme shortcodes do not work

{% hint style="danger" %}
Theme shortcodes do **not** work in the fpw / membersonly template `header` and
`footer` keys. `simpleParse()` deletes every plain `{WORD}` code there before
the full parser runs.

The workaround — PHP constants plus `e107::getThemePref()` — is on
[Auth pages](../standalone/auth-pages.md).
{% endhint %}

## Two shortcodes that are not the theme's

Worth knowing, because they sit next to the theme's own and look alike.

`{LAYOUT_ID}` is **core**: it expands to `"layout-" + name2id(THEME_LAYOUT)`
(`header_default.php:801`) and gives every layout a body class with no theme
code involved. Prefer it over `{BODY_CLASS}` whenever the rule is simply "on
this layout" — see [{BODY_CLASS}](shortcodes/body-class.md).

`{MENU=101}` with a numeric parm is the same as `{MENUAREA=101}` — both call
`renderArea()` (`e107_core/shortcodes/single/menu.php`). The two spellings are
interchangeable, which matters when reading a layout that mixes them.

## Planned

* `{THEME_AUTH_BGIMAGE}` — auth splash image from theme preferences
  (layout-side counterpart to the PHP pref read used in the fpw template).
* Content helpers for the home layout sections.
* Memberdesk helpers for the custom plugins in preparation.
