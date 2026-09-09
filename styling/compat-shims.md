# Compatibility shims

**Status: 100% for what is known — the list grows as plugins are met.**

## What it is

e107 core and its plugins still emit Bootstrap 3 and Bootstrap 4 class names.
Some are simply inert under Bootstrap 5. Others were *reused* by Bootstrap 5 or
Tabler for something else, and those actively break the page.

The shims live in the "Legacy markup fixes" and "sr-only compatibility"
sections of `style.css`. They are theme-level, never brand-level: they have
nothing to do with colour and every brand needs them.

{% hint style="info" %}
Every shim here is temporary. When upstream markup moves to Bootstrap 5 the
matching shim can be deleted. That is why they sit in their own sections rather
than scattered through the file — so there is something to audit.
{% endhint %}

## The shims that ship

### `.sr-only` no longer hides anything

Bootstrap 5 renamed `.sr-only` to `.visually-hidden` and dropped the old name.
Markup still using it now *shows* the text it meant to hide.

Seen in `login_menu_shortcodes.php:105` and `:120`, where the username and
password inputs emit their own `<label class='sr-only'>` — which then duplicates
the visible label the template renders.

The shim reproduces Tabler's `.visually-hidden` rules (`tabler.css:23347-23361`)
under the old class name.

**Removed when:** plugin shortcodes emit `visually-hidden`.

### `.img-responsive` became an aspect-ratio helper

A Bootstrap 3 class for fluid images. Tabler reuses the name for a background
aspect-ratio helper built on a `padding-top` hack, so a real `<img>` carrying it
gets an empty band above it.

The shim neutralises `padding-top` and `background` on `img.img-responsive`
only, so Tabler's own `div.img-responsive` keeps working.

**Removed when:** core templates emit `img-fluid`.

### `size=` on inputs breaks input groups

e107 emits a `size` attribute on many inputs. Neutralising it with
`.form-control[size] { width: 100% }` looks harmless and is not: that selector
scores `0,2,0`, the same as Tabler's
`.input-group > .form-control { width: 1% }`, and `style.css` loads later. The
input then claims the full row and the buttons wrap to a second line.

Two rules ship: the width reset, and a higher-specificity rule restoring
Tabler's flex sizing inside an input group.

{% hint style="warning" %}
The width reset is arguably unnecessary — Tabler's base `.form-control` already
sets `width: 100%`, and an HTML `size` attribute loses to any CSS `width`. It is
kept because removing it has not been tested across every e107 form.
{% endhint %}

**Removed when:** confirmed that no e107 form depends on it.

### Legacy option buttons

`.options .btn-group .btn` in the news view and elsewhere predates Tabler's
`.btn-action` look. Restyled rather than rewritten, because the markup comes
from core.

**Removed when:** the option bars are templated by the theme.

## Classes met in plugin templates

These are fixed by overriding the plugin's template rather than by a shim — see
[Plugins](../plugins/news.md). Listed here because the same names keep
appearing and it is worth knowing what each one became.

| Legacy | Bootstrap 5 | Effect if left |
| --- | --- | --- |
| `sr-only` | `visually-hidden` | text becomes visible |
| `pull-right`, `pull-left` | `float-end`, `float-start` | no float |
| `muted` | `text-muted` | no colour |
| `float-right` | `float-end` | no float |
| `text-right` | `text-end` | no alignment |
| `mr-3`, `ml-3` | `me-3`, `ms-3` | no margin |
| `media`, `media-body`, `media-left`, `media-object` | flex utilities | no layout |
| `label label-primary` | `badge` | unstyled span |
| `nav nav-list` | `list-group` | competing padding rules |
| `list-group` without `list-group-flush` | add the modifier | rounded corners escape the card |

That last one is not a renamed class but the same kind of trap: inside a card
body a list group keeps its own border and rounds its first and last items,
which then poke out of the container. It is what made the side panels look
broken before the plugin templates were overridden.

## Auditing

Grep the plugin templates for the left column of that table:

```
grep -rn "pull-right\|pull-left\|\bmuted\b\|float-right\|text-right\|\bmr-[0-9]\|\bml-[0-9]\|\bmedia\b\|label-primary\|sr-only" e107_plugins/*/templates/
```

Anything that turns up is either a template to override or, if it is markup the
theme cannot reach, a shim to add here.
