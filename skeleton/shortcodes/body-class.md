# {BODY_CLASS}

**Status: done (100%)**

Per-layout classes for the `<body>` element.

## Usage

```html
<body class="{LAYOUT_ID} {BODY_CLASS}" {BODY_ONLOAD} style="--nav-h: 3.5rem">
```

Used once, in [theme.html](../theme-html.md).

## `{BODY_CLASS}` versus `{LAYOUT_ID}`

Two different things sit next to each other on that tag.

`{LAYOUT_ID}` is a **core** shortcode. It expands to
`"layout-" + name2id(THEME_LAYOUT)` (`header_default.php:801`), so every layout
gets a handle — `layout-home`, `layout-3columns` — with no theme code at all.
It is what scopes a brand rule to one layout:

```css
.layout-home .main-header { background: transparent; }
```

`{BODY_CLASS}` is the theme's own, for classes that are a *decision* rather
than a fact about which layout is rendering.

Prefer `{LAYOUT_ID}` when the rule is simply "on this layout". Reach for
`{BODY_CLASS}` when the class expresses something the layout name does not.

## Code

```php
	// {BODY_CLASS}
	// Per-layout <body> classes, used in theme.html:
	//   <body class="{LAYOUT_ID} {BODY_CLASS}" {BODY_ONLOAD}>
	//
	// IMPORTANT: THEME_LAYOUT must be read HERE via defset(), at parse time.
	// defset() at call time, never cached in __construct(). See docs: theme_shortcodes.php.
	function sc_body_class($parm = null)
	{
		$classes = array();

		switch(defset('THEME_LAYOUT'))
		{
			case 'home':
				$classes[] = 'body-marketing';
				$classes[] = 'body-gradient';
				break;

		}

		return implode(' ', $classes);
	}
```

## Adding a layout

Add a `case` to the switch. Only `home` has classes today — `body-marketing`
and `body-gradient`, both from `tabler-marketing.css`.

## Notes

{% hint style="danger" %}
`THEME_LAYOUT` via `defset()` inside the method — see
[theme_shortcodes.php](../theme-shortcodes.md).
{% endhint %}
