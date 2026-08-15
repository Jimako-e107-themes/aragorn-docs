# Fixing invisible badge text (`bg-*` vs. `text-bg-*`)

## Symptom

A Tabler `.badge` renders as a colored blob with **no visible text or
number** — the markup and shortcode output are correct, the color is
applied, but the label itself cannot be read. Two variations were found in
Aragorn:

* `<span class="badge bg-secondary rounded-pill badge-secondary">2</span>`
  (a forum topic/reply count) — completely invisible.
* `<span class="badge bg-primary">0</span>` (a news comment count in a
  list-group) — visible but very low contrast, easy to mistake for an empty
  pill at small size.

## Root cause

Tabler's `.badge` sets its **own** text color independently of whatever
background utility class is added next to it:

```css
.badge {
    --tblr-badge-color: var(--tblr-secondary);
    color: var(--tblr-badge-color);
    ...
}
```

`bg-secondary` / `bg-primary` are plain **background-only** utilities —
they never touch `--tblr-badge-color`. So:

* `badge bg-secondary` → background `var(--tblr-secondary)` (`#6b7280`),
  text *also* `var(--tblr-secondary)` (`#6b7280`) — identical color on
  color, text fully invisible.
* `badge bg-primary` → background `var(--tblr-primary)` (`#066fd1`), text
  stays the default secondary gray (`#6b7280`) — different colors, but a
  muted gray on a saturated blue is low-contrast and hard to read at badge
  font sizes.

{% hint style="info" %}
This is unrelated to font-size or the badge's `em`-based sizing
(`--tblr-badge-font-size: 0.85714285em`) — that only matters inside
ancestors using legacy `style="font-size:0"` whitespace-collapse hacks
(a separate, real issue in some old e107 list markup), which is worth
ruling out first but was **not** the cause here.
{% endhint %}

## The fix

Use Bootstrap/Tabler's paired utility class **`text-bg-*`** instead of
`bg-*`. Unlike `bg-*`, `text-bg-*` sets background **and** a contrasting
text color together, specifically designed for this exact combination:

```css
.text-bg-secondary {
    color: #f9fafb !important;
    background-color: rgba(var(--tblr-secondary-rgb), var(--tblr-bg-opacity, 1)) !important;
}
```

Change every colored badge in theme templates from:

```html
<span class="badge bg-secondary rounded-pill badge-secondary">2</span>
```

to:

```html
<span class="badge text-bg-secondary rounded-pill">2</span>
```

(`badge-secondary` without `bg-` is a leftover Bootstrap 4 class name that
does nothing in Bootstrap 5/Tabler — safe to drop while you're there.)

Applied in the news templates
(`badge bg-primary` → `badge text-bg-primary` in
`news_template.php`, `news_grid_template.php`, `news_menu_template.php` —
see [News templates](../plugins/news.md)). The same swap is needed in any
other plugin template that colors a `.badge` with a plain `bg-*` class —
forum topic/reply counts were the other confirmed case.

## Why not just override `.badge` color in CSS

A single `.badge { color: ... }` rule in `style.css` would fix one variant
and break the others (an override can't know which background color it's
paired with on a given badge). `text-bg-*` is the correct pairing
mechanism because it's defined per color and already ships in Tabler's
CSS — the fix is a markup change (swap the class), not new CSS.

## Checklist for future badges

* Never write `badge bg-<color>` alone in a template.
* Always use `badge text-bg-<color>` for solid colored badges.
* Drop legacy `badge-<color>` (no `bg-`/`text-bg-` prefix) classes on sight
  — Bootstrap 4 leftovers, inert in Bootstrap 5.
