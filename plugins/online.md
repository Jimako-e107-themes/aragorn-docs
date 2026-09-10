# Online

**Status: done (100%) — both menus rewritten for Bootstrap 5.**

## What the theme overrides

```
templates/online/online_menu_template.php
```

covering all three top-level keys the plugin defines: `lastseen`, `default`
and `extended`.

## Why

The plugin's markup is Bootstrap 3/4 era. Two problems, both visible in the
side panels:

**Missing `list-group-flush`.** `online_menu_template.php:14` opens the last
seen menu with `<ul class='list-group lastseen-menu'>`. Inside an accordion
body that keeps the list's own border and rounds its first and last items,
which then poke out of the card.

**Dead class names.** `muted`, `pull-right`, `float-right`, `text-right`,
`mr-3`, `media` / `media-body` / `media-left` / `media-object`, and
`label label-primary` — all inert or repurposed under Bootstrap 5. The
`{LASTSEEN_DATE}` timestamps, for instance, sit right only because the `<li>`
carries `d-flex justify-content-between`; `pull-right` contributes nothing.

## What changed

| Upstream | Override |
|---|---|
| `<ul class='list-group'>` | `<div class='list-group list-group-flush'>` |
| `<li class='list-group-item'>` | `<div class='list-group-item'>` |
| `muted` | `text-muted` |
| `pull-right`, `pull-left`, `float-right` | dropped or `float-end` |
| `text-right` | `text-end` |
| `mr-3` | `me-3` |
| `media` family | flex utilities |
| `label label-primary` | dropped — `badge` was already there |
| `LAN_ONLINE_1 . "…"` | `{LAN=ONLINE_1}` |

`<ul>/<li>` became `<div>` deliberately: `.list-group-flush > .list-group-item`
is a direct-child selector, and several of the plugin's wrappers nested an
extra `<ul>` between them.

## Two gotchas specific to this plugin

{% hint style="warning" %}
**`lastseen_menu.php` checks the v1 path first.** Before calling
`getTemplate()` it tests `THEME.'online_menu_template.php'` — the theme root,
no subdirectory — and requires that file instead if it exists. A leftover there
silently wins over `templates/online/`.
{% endhint %}

{% hint style="warning" %}
**`$ONLINE_MENU_WRAPPER` is a separate variable.** `_getTemplate()` reads only
`$ONLINE_MENU_TEMPLATE`. Whether the wrappers defined in the theme file are
read at all depends on how `online_menu.php` obtains them — **unverified**. If
the item markup looks right but the labels and badges around it still carry
`pull-right`, this is why.
{% endhint %}

The general mechanics are in
[Overriding a plugin's menu template](../guides/overriding-plugin-templates.md).

## Upstream

Both the missing `list-group-flush` and the dead class names are worth
reporting — they affect every Bootstrap 5 theme, not only this one.
