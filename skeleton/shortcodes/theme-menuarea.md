# {THEME_MENUAREA}

**Status: done (100%)**

Renders a menu area from one chosen layout, whatever layout the current page
uses. The mobile drawers are configured once instead of five times.

## Usage

```
{THEME_MENUAREA=101}
```

The parm is the menu area number. `{MENUAREA=101}` stays in the layout the
menus are assigned on — `ARAGORN_DRAWER_LAYOUT`, currently `3columns` — and
every other layout uses this shortcode.

## Why it exists

e107 stores menu placement per layout: `e_menu` loads menus for `THEME_LAYOUT`
only (`menu_class.php:407`) and `renderArea()` takes no layout parameter. Its
single parm is the `:echo` suffix.

That is right for page content and wrong for the drawers, which are identical
on every layout by design.

The mechanism, the security reasoning behind subclassing `e_menu`, and its one
failure mode are on
[Shared menu areas](../../guides/shared-menu-areas.md).

## Code

```php
	// {THEME_MENUAREA=101}
	// Renders a menu area from the layout named in ARAGORN_DRAWER_LAYOUT,
	// whatever layout the current page uses.
	//
	// Core has no equivalent: e_menu loads menus for THEME_LAYOUT only
	// (menu_class.php:407) and renderArea() takes no layout parameter, so the
	// same menu has to be placed again in Menu Manager for every layout. The
	// mobile drawers are identical everywhere, which makes that busywork and a
	// source of drift.
	//
	// Areas addressed this way disappear from Menu Manager on the layouts that
	// use this shortcode, which is intended: the drawers are configured once,
	// on the source layout, and mirrored everywhere else.
	function sc_theme_menuarea($parm = null)
	{
		$area = (int) $parm;

		if($area < 1)
		{
			return '';
		}

		if($this->drawerMenu === null)
		{
			$this->drawerMenu = new aragorn_menu();
			$this->drawerMenu->initFromSourceLayout();
		}

		return $this->drawerMenu->renderArea($area);
	}
```

The reader:

```php
if(!class_exists('aragorn_menu', false))
{
	class aragorn_menu extends e_menu
	{
		/**
		 * Load the source layout's menus into eMenuActive, applying the same
		 * visibility rules core applies in init().
		 *
		 * @return void
		 */
		public function initFromSourceLayout()
		{
			// Defined in theme.php, which is always loaded before this file.
			$layout = defset('ARAGORN_DRAWER_LAYOUT', '3columns');

			// e_menu::getDataLegacy() stores the DEFAULT layout's menus with an
			// empty menu_layout rather than the layout's own name, so the name
			// has to be translated before it can be matched against the column.
			// Querying for the literal '3columns' finds nothing while 3columns
			// is the default.
			if($layout === e107::getPref('sitetheme_deflayout'))
			{
				$layout = '';
			}

			$layout = e107::getParser()->toDB($layout);

			$rows = e107::getDb()->retrieve('menus', '*',
				"menu_location > 0 AND menu_layout = '" . $layout . "' ORDER BY menu_location, menu_order",
				true);

			// The source layout is no longer the default one, so its menus are
			// not stored under its name any more. Fall back to whatever IS the
			// default: those are the rows the admin has been editing, so the
			// drawers keep working. theme::init() warns admins about the
			// mismatch, so this stays a soft landing rather than a silent one.
			if(empty($rows) && $layout !== '')
			{
				$rows = e107::getDb()->retrieve('menus', '*',
					"menu_location > 0 AND menu_layout = '' ORDER BY menu_location, menu_order",
					true);
			}

			if(empty($rows))
			{
				return;
			}

			foreach($rows as $row)
			{
				if($this->isVisible($row))
				{
					$this->eMenuActive[$row['menu_location']][] = $row;
				}
			}

			// e107::getRender()->eMenuTotal is deliberately left alone: it
			// describes the CURRENT layout's areas, and overwriting it here
			// would feed wrong counts to any menu or template that reads it.
		}
	}
}
```

## Notes

{% hint style="warning" %}
Areas addressed this way disappear from Menu Manager on those layouts. Menu
Manager finds areas with a regex anchored on `{MENU` or `{MENUAREA`
(`menumanager_class.php:2547`), and a prefixed name never matches.

That is intended — but at least one layout must keep the plain form, or the
menus cannot be assigned at all.
{% endhint %}

`aragorn_menu` is instantiated once per request and held on the shortcode
object, so three drawer areas cost one query rather than three.
