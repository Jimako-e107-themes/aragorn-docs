# Shared menu areas

**Status: 90% — works, with one known failure mode that warns instead of breaking.**

## The problem

e107 stores menu placement per layout. A menu dropped into area 101 on one
layout does not appear in area 101 on another — each layout has to be
configured separately in Menu Manager.

That is reasonable for page content and wrong for the mobile drawers, which are
identical on every layout by design. Configuring the same three menus five
times is busywork, and the five copies drift.

There is no core mechanism for this. `e_menu` loads menus for `THEME_LAYOUT`
only (`menu_class.php:407`) and `renderArea()` takes no layout parameter — its
single parm is the `:echo` suffix.

## `{THEME_MENUAREA=101}`

The theme shortcode renders a menu area from one chosen layout, whatever layout
the current page uses. The drawers are configured once, on the layout named in
`ARAGORN_DRAWER_LAYOUT`, and mirrored everywhere else.

| Layout | Drawer areas use |
| --- | --- |
| 3columns | `{MENUAREA=101}` — the source of truth |
| full, page, sidebar, home | `{THEME_MENUAREA=101}` |

{% hint style="info" %}
Areas addressed with `{THEME_MENUAREA}` disappear from Menu Manager on those
layouts. That is intended, not a side effect: Menu Manager finds areas with
`preg_match_all("/\{(?:MENU|MENUAREA)=([\d]{1,3})(:[\w\d]*)?\}/", …)`
(`menumanager_class.php:2547`), and the `\{` anchor means a prefixed name never
matches. At least one layout must keep the plain form, or the menus cannot be
assigned at all.
{% endhint %}

## How it works

`aragorn_menu` in `theme_shortcodes.php` extends `e_menu` and loads the source
layout's menus into `eMenuActive`, then hands them to the inherited
`renderArea()`.

```php
class aragorn_menu extends e_menu
{
	public function initFromSourceLayout()
	{
		$layout = defset('ARAGORN_DRAWER_LAYOUT', '3columns');

		if($layout === e107::getPref('sitetheme_deflayout')) { $layout = ''; }

		$rows = e107::getDb()->retrieve('menus', '*',
			"menu_location > 0 AND menu_layout = '" . e107::getParser()->toDB($layout) . "'
			 ORDER BY menu_location, menu_order", true);
		…
	}
}
```

Two details carry the weight.

**Subclassing, not querying by hand.** `isVisible()` is `protected`, and it is
what enforces `menu_class` (userclass permissions) and `menu_pages` (per-URL
visibility). `renderMenu()` only checks userclass for custom page menus, never
for plugin menus — so a hand-rolled query and render loop would show
members-only menus to guests. Extending `e_menu` is what keeps core's rules.

**The empty `menu_layout`.** The default layout's menus are stored with an
*empty* `menu_layout`, not with the layout's name. That mapping happens in one
place:

```php
// menu_class.php:407
$menu_layout_field = THEME_LAYOUT != e107::getPref('sitetheme_deflayout') ? THEME_LAYOUT : "";
```

So querying for the literal `'3columns'` finds nothing while 3columns is the
default. The translation above is not optional.

## The failure mode

Because placement is tied to the *role* of being default rather than to a
layout name, changing the site default breaks the link: the source layout's
menus are no longer stored under its name.

Two things absorb this.

**A fallback.** If the query returns nothing, `initFromSourceLayout()` retries
against whatever *is* the default. Those are the rows the administrator has
been editing, so the drawers keep working.

**A warning.** `theme::checkDrawerLayout()` adds a message for main
administrators when the default layout is not `ARAGORN_DRAWER_LAYOUT`. It does
not correct the preference: that would overwrite a deliberate choice and write
to the database on a front-end request.

Between them the behaviour degrades from "silently empty" to "still working,
with an explanation".

## Cost

One query per page. The `aragorn_menu` instance is held on the shortcode object
for the request, so three drawer areas cost one query, not three.

## What it rests on

Two pieces of core that are not published API:

- `e_menu::isVisible()` is `protected` and `$eMenuActive` is `public`
- the default layout stores an empty `menu_layout`

If either changes, the drawers go empty — not open. The failure direction is
safe: nothing leaks.
