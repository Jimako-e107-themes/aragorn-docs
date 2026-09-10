# Theme shortcodes

Custom shortcodes live in `theme_shortcodes.php` (class extending
`e_shortcode`). They are available in all theme layouts and templates parsed
by the full parser.

{% hint style="danger" %}
Theme shortcodes do **not** work in the fpw / membersonly template
`header` / `footer` keys — `simpleParse()` deletes every plain `{WORD}`
code there before the full parser runs. See
[Auth pages](../standalone/auth-pages.md) for the workaround (PHP constants +
`e107::getThemePref()`).
{% endhint %}

## Implemented

| Shortcode | Purpose | Status |
|---|---|---|
| `{ADVANCED_LOGIN_LINK}` | styled link to the login page | done |
| `{ADVANCED_SIGNUP_LINK}` | styled link to the signup page | done |
| `{ADVANCED_FPW_BUTTON}` | styled forgot-password button | done |
| `{BODY_CLASS}` | per-layout `<body>` classes | done |
| `{PAGE_CLASS}` | per-layout classes for the `.page` wrapper in theme.html | done |
| `{HEADER}` | loads `headers/header_<variant>.html` partial | done |
| `{FOOTER}` | loads `footers/footer_<variant>.html` partial | done |
| `{THEME_TOGGLE}` | dark/light switch, Tabler's two-button snippet | done |
| `{BOTTOM_NAV}` | mobile bottom bar; opens the offcanvas drawers | done |
| `{TILES}` | shortcut tiles under the header | done |
| `{THEME_MENUAREA=n}` | a menu area from one layout, rendered on all of them | done |

Why the three `ADVANCED_*` shortcodes exist instead of the core equivalents
(link-in-sentence LANs, `user_reg` visibility, inline SVG in the submit
button) is documented in
[Why the theme defines custom auth shortcodes](../guides/custom-auth-shortcodes.md).

## `{BOTTOM_NAV}`

The mobile navigation bar, rendered from `theme.html` so it is present on
every layout that does not suppress it. Items are fixed in `sc_bottom_nav()`:
a home link plus three triggers that open the offcanvas drawers.

{% hint style="danger" %}
Every item with a `panel` opens one of `#panelLeft`, `#panelUser`,
`#panelLatest`. A layout that renders the bar without those panels produces
`Cannot read properties of undefined (reading 'backdrop')` — Bootstrap's
`BaseComponent` returns early on a missing target, leaving `_config` undefined
before `Offcanvas` reads `_config.backdrop`.

The suppression list in `sc_bottom_nav()` and the panel block in the layouts
are therefore a pair. Adding a layout means updating both, or neither.
{% endhint %}

The bar is `d-lg-none`, and `style.css` adds bottom padding only on pages that
actually render it — otherwise every mobile page would get dead space.

## `{THEME_MENUAREA=n}`

Renders a menu area from one chosen layout, whatever layout the current page
uses, so the drawers are configured once instead of five times.

`{MENUAREA=101}` stays in the layout the menus are assigned on; every other
layout uses `{THEME_MENUAREA=101}`. The mechanism, its security reasoning and
its one failure mode are on
[Shared menu areas](../guides/shared-menu-areas.md).

{% hint style="warning" %}
Menu Manager finds areas with a regex anchored on `{MENU` or `{MENUAREA`
(`menumanager_class.php:2547`), so an area addressed this way disappears from
its layout tab. That is intended — but it means at least one layout must keep
the plain form, or the menus cannot be assigned at all.
{% endhint %}

## Reading `THEME_LAYOUT`

{% hint style="danger" %}
In every shortcode that branches on the layout — `{HEADER}`, `{FOOTER}`,
`{BOTTOM_NAV}`, `{TILES}`, `{BODY_CLASS}`, `{PAGE_CLASS}` — read
`THEME_LAYOUT` via `defset()` **at call time, inside the method**. The batch
is a singleton created on the first `getScBatch('theme')` call anywhere, and a
plugin or menu can instantiate it before `e_theme::initThemeLayout()` has
defined the constant. Caching it in the constructor works only sometimes,
which is worse than not working at all.
{% endhint %}

## Planned

The list below is a snapshot — more shortcodes will be added as the home
and memberdesk layouts develop.

* `{THEME_AUTH_BGIMAGE}` — auth splash image from theme preferences
  (layout-side counterpart to the PHP pref read used in the fpw template).
* Content helpers for the home layout sections.
* Memberdesk helpers for the custom plugins in preparation.
