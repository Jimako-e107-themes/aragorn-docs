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
	// {THEME_MENUAREA=101} - see docs: {THEME_MENUAREA}.
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
		 * @return void
		 */
		public function initFromSourceLayout()
		{
			$layout = defset('ARAGORN_DRAWER_LAYOUT', '3columns');

			// The default layout is stored as an empty menu_layout.
			if($layout === e107::getPref('sitetheme_deflayout'))
			{
				$layout = '';
			}

			$layout = e107::getParser()->toDB($layout);

			$rows = e107::getDb()->retrieve('menus', '*',
				"menu_location > 0 AND menu_layout = '" . $layout . "' ORDER BY menu_location, menu_order",
				true);

			// Fallback for a changed default layout; theme::init() warns about it.
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

			// eMenuTotal is left alone on purpose - it describes the CURRENT layout.
		}
	}
}
```

## Notes

**`eMenuTotal` is left alone.** `e_menu::init()` sets
`e107::getRender()->eMenuTotal` while loading, and a theme or menu can read it
to know how many menus an area holds. `initFromSourceLayout()` deliberately
does not touch it: it describes the **current** layout's areas, and
overwriting it with the source layout's counts would feed wrong numbers to
everything else on the page.

**The layout name is escaped.** It reaches SQL, so it goes through `toDB()`.
Today it comes from a constant and the risk is nil — but if the source layout
ever becomes a theme preference, the escaping is already in place rather than
something to remember.

{% hint style="warning" %}
Areas addressed this way disappear from Menu Manager on those layouts. Menu
Manager finds areas with a regex anchored on `{MENU` or `{MENUAREA`
(`menumanager_class.php:2547`), and a prefixed name never matches.

That is intended — but at least one layout must keep the plain form, or the
menus cannot be assigned at all.
{% endhint %}

`aragorn_menu` is instantiated once per request and held on the shortcode
object, so three drawer areas cost one query rather than three.
