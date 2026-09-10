# {THEME_TOGGLE}

**Status: done (100%)**

The dark/light switch in the header.

## Usage

```html
<div class="nav-item">
	{THEME_TOGGLE}
</div>
```

Used in both header partials.

## How it works

Two buttons, not one. Tabler shows whichever matches the current scheme through
`.hide-theme-dark` and `.hide-theme-light`, so no state has to be tracked in
the markup and no positioning is needed — a `position: fixed` here would pull
the control out of the header.

The buttons carry `data-bs-theme-value`; the handler is `js/theme-toggle.js`.

{% hint style="warning" %}
The click handler and the pre-paint script `js/tabler-theme.min.js` must use
the **same** localStorage key, `tabler-theme`. The pre-paint script runs in
`<head>`, before the browser starts rendering `<body>`, and sets
`data-bs-theme` on `<html>` — that is what prevents a flash of the wrong
scheme. A key mismatch means the toggle works and the choice is forgotten on
the next page load.
{% endhint %}

## Code

```php
	// {THEME_TOGGLE}
	// Dark/light mode toggle, placed in header_3columns.html inside a
	// .nav-item. Not available on layouts where sc_header() suppresses
	// {HEADER} (auth, memberdesk) - acceptable, those are transient pages.
	//
	// Markup follows Tabler's layout-horizontal.html: two buttons using
	// .hide-theme-dark / .hide-theme-light, each visible only in the OTHER
	// mode, so the icon swap needs no custom CSS. Tabler's own markup uses
	// <a href="?theme=dark">; replaced here with data-bs-theme-value, read
	// by js/theme-toggle.js - no navigation, no query string.
	function sc_theme_toggle($parm = null)
	{
		$labelDark  = deftrue('LAN_THEME_TOGGLE_DARK', 'Enable dark mode');
		$labelLight = deftrue('LAN_THEME_TOGGLE_LIGHT', 'Enable light mode');

		return '
<div class="theme-toggle-group">
	<button type="button" class="nav-link px-0 hide-theme-dark" data-bs-theme-value="dark" aria-label="' . $labelDark . '">
		<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="icon">
			<path d="M12 3c.132 0 .263 0 .393 0a7.5 7.5 0 0 0 7.92 12.446a9 9 0 1 1 -8.313 -12.454z"></path>
		</svg>
	</button>
	<button type="button" class="nav-link px-0 hide-theme-light" data-bs-theme-value="light" aria-label="' . $labelLight . '">
		<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="icon">
			<path d="M12 12m-4 0a4 4 0 1 0 8 0a4 4 0 1 0 -8 0"></path>
			<path d="M3 12h1m8 -9v1m8 8h1m-9 8v1m-6.4 -15.4l.7 .7m12.1 -.7l-.7 .7m0 11.4l.7 .7m-12.1 -.7l-.7 .7"></path>
		</svg>
	</button>
</div>';
	}
```

## Notes

The two inline SVGs are Tabler's own snippet for this control, kept verbatim
rather than swapped for the icon webfont — see
[Theme assets](../theme-assets.md).

`.theme-toggle-group` in `style.css` only sets `display: flex`.
