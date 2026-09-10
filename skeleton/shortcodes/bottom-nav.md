# {BOTTOM_NAV}

**Status: 85% — works; item set and per-layout suppression still moving.**

The mobile navigation bar. Rendered from `theme.html`, so it is present on
every layout that does not suppress it, and hidden above `lg`.

## Usage

```
{BOTTOM_NAV}
```

No parms. The items are defined in `sc_bottom_nav()`.

## What it renders

A fixed bar with four targets: a home link and three triggers that open the
offcanvas drawers.

| Item | Opens |
|---|---|
| Home | — plain link |
| Preklady | `#panelLeft` |
| Najnovšie | `#panelLatest` |
| Účet | `#panelUser` |

Why the drawers are reached this way rather than by a hamburger, and how the
three panels relate to the desktop columns, is on
[3columns on mobile](../../layouts/3columns-mobile.md).

## The pairing rule

{% hint style="danger" %}
Every item with a panel opens one of `#panelLeft`, `#panelUser`,
`#panelLatest`. A layout that renders the bar without those panels produces:

```
Uncaught TypeError: Cannot read properties of undefined (reading 'backdrop')
```

Bootstrap's `BaseComponent` returns early when the target element is missing,
which leaves `_config` undefined before `Offcanvas` reads `_config.backdrop`.

The suppression list in `sc_bottom_nav()` and the panel block in the layouts
are a **pair**. Adding a layout means updating both, or neither.
{% endhint %}

## Bottom padding

`style.css` adds `padding-bottom` to `body:has(.nav-bottom)` below `lg` — only
on pages that actually render the bar, otherwise every mobile page would get
dead space above the fold.

## No code snippet yet

{% hint style="info" %}
`sc_bottom_nav()` is not reproduced here. The item list and the suppression
list are still being adjusted, and a snippet that goes stale is worse than no
snippet.

It will be added once both stop moving. See
[Documentation conventions](../../development/README.md) for why snippets are
whole-file copies rather than excerpts.
{% endhint %}

## Open

* The four items are hardcoded. Whether they should become configurable is
  undecided — the bar is arguably part of the theme's design, like the drawers
  it opens.
* The suppression list is a blacklist. A whitelist of layouts that carry the
  panels would fail safe instead.
