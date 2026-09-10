# {HEADER}

**Status: done (100%)**

Loads a header partial from `headers/`, chosen by the current layout.

## Usage

```
{---HEADER---}
```

The marker lives in [theme.html](../theme-html.md), not in the layouts. Core
expands it during `e_theme::loadLayout()` — **before** the layout is parsed and
before Menu Manager scans it, which is what makes `{MENUAREA}` work inside a
partial. See
[Header & footer partials with working menu areas](../../guides/header-footer-partials.md).

## Which partial each layout gets

| Layout | Partial |
|---|---|
| `home` | `headers/header_default.html` |
| `auth`, `memberdesk`, `raw`, `error` | none |
| everything else | `headers/header_3columns.html` |

`auth` and `memberdesk` get nothing at all, for different reasons. On `auth`
the partial would render inside `.page.page-center`, whose flex centering would
break it — and Tabler auth pages carry no navbar anyway, so the brand block
lives in `auth_layout.html` instead. On `memberdesk` the vertical sidebar
replaces the horizontal header.

`header_default.html` is not the default despite the name — the `default:`
branch returns the `_3columns` variant. It is the two-row variant used by
`home`. See [Header & footer partials](../../layouts/header-footer.md).

## Code

```php
	// {HEADER} - see docs: {HEADER}.
	function sc_header()
	{
		switch(defset('THEME_LAYOUT'))
		{
			case 'error':
				return '';

			case 'auth':
			case 'memberdesk':
				return '';

			case 'home':
			$headerName = 'header_default.html';
			break;

			default:
				$headerName = 'header_3columns.html';
				break;
		}

		// __DIR__ on purpose - it always resolves to the theme this file
		// belongs to, in every context:
		// - THEME constant points to the ADMIN theme in the admin area (the
		//   Menu Manager builds its "Add" area list there),
		// - the sitetheme pref ignores a user-selected theme (USERTHEME),
		// - USERTHEME is forced off while the Menu Manager is active.
		$headerPath = __DIR__.'/headers/'.$headerName;

		return file_exists($headerPath) ? file_get_contents($headerPath) : '';
	}
```

## Notes

{% hint style="danger" %}
`__DIR__`, not `THEME`. In an admin context — Menu Manager's preview, for one —
`THEME` points at the *admin* theme, and the partial would not be found.
{% endhint %}

{% hint style="danger" %}
`THEME_LAYOUT` is read with `defset()` inside the method. The shortcode batch
is a singleton created on the first `getScBatch('theme')` call anywhere, and a
plugin or menu can instantiate it before `e_theme::initThemeLayout()` has
defined the constant. Caching it in the constructor works only sometimes.
{% endhint %}

Partials must contain no HTML comments and keep every shortcode on a single
line — the layout renderer parses line by line.
