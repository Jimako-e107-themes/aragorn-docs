# {PAGE_CLASS}

**Status: done (100%)**

Per-layout classes for the shared `.page` wrapper.

## Usage

```html
<div class="page {PAGE_CLASS}">
```

Used once, in [theme.html](../theme-html.md). The wrapper is shared by every
layout — layout files start *inside* it, at `.page-wrapper` — so anything that
has to sit on `.page` itself comes from here.

The case that made it necessary is `auth`, which needs Tabler's `page-center`
to vertically centre the login card.

## Code

```php
	// {PAGE_CLASS}
	// Per-layout classes for the .page wrapper in theme.html. The wrapper
	// itself lives in theme.html because the {---HEADER---} partial must
	// render INSIDE .page (Tabler structure: .page > header > page-wrapper).
	// Same rule as {BODY_CLASS}: read THEME_LAYOUT at call time via defset().
	function sc_page_class($parm = null)
	{
		switch(defset('THEME_LAYOUT'))
		{
			case 'auth':
				return 'page-center';
		}

		return '';
	}
```

## Notes

{% hint style="danger" %}
`THEME_LAYOUT` via `defset()` inside the method — see
[theme_shortcodes.php](../theme-shortcodes.md).
{% endhint %}
