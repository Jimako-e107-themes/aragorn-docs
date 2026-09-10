# {FOOTER}

**Status: done (100%)**

Loads a footer partial from `footers/`, chosen by the current layout. Same
mechanism as [{HEADER}](header.md).

## Usage

```
{---FOOTER---}
```

Expanded by core during `e_theme::loadLayout()`, from the marker in
[theme.html](../theme-html.md).

## Which partial each layout gets

| Layout | Partial |
|---|---|
| `memberdesk` | `footers/footer_small.html` |
| `auth`, `raw`, `error` | none |
| everything else | `footers/footer_3columns.html` |

`footer_default.html` is currently unused and kept as a worked example.

## Code

```php
	// {FOOTER}
	// Loads footers/footer_<variant>.html - same mechanism as {HEADER}.
	// Shortcodes inside the partial ({MENUAREA=101}, {NAVIGATION=...},
	// {XURL_ICONS}, ...) are parsed by the layout renderer, and menu areas
	// are detected by the Menu Manager, because the partial is merged into
	// the layout before both happen.
	// The switch is prepared for per-layout footers; for now every layout
	// uses the default.
	function sc_footer()
	{
		switch(defset('THEME_LAYOUT'))
		{
			case 'error':
				return '';

			// Auth pages: no site footer inside the centered auth page.
			case 'auth':
				return '';

			// Memberdesk: reduced footer variant.
			case 'memberdesk':
				$footerName = 'footer_small.html';
				break;

			default:
				$footerName = 'footer_3columns.html';
				break;
		}

		// __DIR__ on purpose - see sc_header() note.
		$footerPath = __DIR__.'/footers/'.$footerName;

		return file_exists($footerPath) ? file_get_contents($footerPath) : '';
	}
```

## Notes

The same two rules as `{HEADER}`: `__DIR__` rather than `THEME`, and
`THEME_LAYOUT` read with `defset()` at call time.

{% hint style="warning" %}
Containers must agree between the layout, its header and its footer. Tabler
caps `container-xl` and `container-xxl` at the same 1320px above 1400px, so a
mismatch stays invisible until someone raises the cap or switches to fluid —
and then the footer is narrower than the content.
{% endhint %}
