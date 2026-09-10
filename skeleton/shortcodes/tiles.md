# {TILES}

**Status: 40% — renders; content and per-layout rules unfinished.**

Shortcut tiles under the header.

## Usage

```
{TILES}
```

No parms.

## How it is suppressed

Per layout, on the same principle as `{HEADER}` and `{FOOTER}` — but **not by
the same mechanism**. Those two are merged into the layout by core during
`e_theme::loadLayout()`, before the layout is parsed. `{TILES}` is an ordinary
shortcode expanded during the layout parse, so it decides for itself at call
time.

That difference matters for one reason: like every layout-aware shortcode, it
must read `THEME_LAYOUT` via `defset()` **inside the method**. See
[theme_shortcodes.php](../theme-shortcodes.md).

## No code snippet yet

{% hint style="info" %}
`sc_tiles()` is not reproduced here. The tile set is unfinished, so a snippet
would go stale before anyone read it.
{% endhint %}

## Open

* Tile content and icons.
* Which layouts show the tiles at all.
* Whether the tiles are theme design or site content — the same question as
  for [{BOTTOM_NAV}](bottom-nav.md), and it should get the same answer.
