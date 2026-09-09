# 3columns

**Status: 95% — the default layout. Ratio and containers settled.**

## What it is

The site's main layout and the one registered as `default` in `theme.xml`. A
catalogue column on the left, content in the middle, account and activity on
the right. Below `lg` all three collapse into one column and the two side
panels become mobile drawers.

It is also the layout the drawer menus are configured on — see
[Shared menu areas](../guides/shared-menu-areas.md).

## The 5-14-5 ratio

The twelve-column grid cannot express this layout. Its two symmetric options
are 3-6-3 (25 / 50 / 25), which leaves the side columns too wide, and 2-8-2
(16.7 / 66.7 / 16.7), which makes them too narrow. The wanted ratio is
5-14-5 of twenty-four: **20.83 / 58.33 / 20.83**.

Three classes in `style.css` cover it:

```css
@media (min-width: 992px) {
  .row > .col24-lg-5  { flex: 0 0 auto; width: 20.8333333333%; }
  .row > .col24-lg-14 { flex: 0 0 auto; width: 58.3333333333%; }
  .row > .col24-lg-19 { flex: 0 0 auto; width: 79.1666666667%; }
}
```

Below `lg` no rule is needed — Bootstrap's `.row > *` already sets
`width: 100%`, so the columns stack by themselves.

{% hint style="info" %}
These are **additional** classes, not a redefinition of the grid. A `col-md-6`
in a nested row inside `col24-lg-14` is still half of that column, because
nested rows are independent and Bootstrap's own grid is untouched.
{% endhint %}

### Why not recompile Bootstrap at 24 columns

Setting `$grid-columns: 24` is the obvious alternative and was rejected:

- `col-md-6` would mean 25% everywhere, so every piece of core and plugin
  markup would need a remapping layer — and any class the layer missed would
  silently render at half width.
- Tabler is loaded as compiled CSS from a CDN with SRI. A custom build means
  self-hosting and a rebuild on every Tabler upgrade.
- Three values are needed. A full 24-column system generates around 144
  classes to use three of them.

`col24-lg-19` is currently unused; it is there for a two-column layout that
wants a side column of the same width as this one.

## Containers

The layout, `header_3columns.html` and `footer_3columns.html` all use
`container-fluid`. All three must agree — Tabler caps `container-xl` and
`container-xxl` at the same 1320px above 1400px, so a mismatch stays invisible
until someone raises the cap or switches to fluid, and then the footer is
suddenly narrower than the content.

### The gap on the left

`container-fluid` exposes a Tabler rule that is harmless with a centred
container (`tabler.css:13884`):

```css
@media (min-width: 992px) {
  :root, :host { margin-left: calc(100vw - 100%); margin-right: 0; }
}
```

It reserves the scrollbar width as a left margin so a centred page does not
jump sideways when a scrollbar appears. With a fluid container it offsets the
whole page, header included, by that width — a visible gap down the left edge.

`style.css` resets the margin and uses `scrollbar-gutter: stable` instead,
which keeps the anti-jump behaviour without the offset.

## The side panels

Both columns use `offcanvas-lg`: a real column from `lg` up, a drawer below it.
The drawer is opened from the bottom navigation bar, which is itself
`d-lg-none`.

```html
<div class="col24-lg-5">
  <div class="offcanvas-lg offcanvas-start col-sticky" tabindex="-1" id="panelLeft">
    <div class="offcanvas-header d-lg-none"> … </div>
    <div class="accordion" id="accLeft">
      {SETSTYLE=accordion-left}
      {MENUAREA=101}
    </div>
  </div>
</div>
```

Three ids matter and are part of the styling contract:
`#panelLeft`, `#panelUser`, `#panelLatest` for the drawers, and
`#accLeft`, `#accUser`, `#accLatest` for the accordions inside them. See
[Brand files](../styling/brand-files.md).

`col-sticky` — not `sticky-top` — caps the column at viewport height and gives
it its own scroll. `sticky-top` pins to `top: 0`, which would slide the column
under the sticky header.

## Menu areas

| Area | Region |
| --- | --- |
| 101 | left panel — catalogue |
| 102 | right panel — account |
| 103 | right panel — activity |

This layout uses `{MENUAREA=n}` directly, unlike the others. It is the source
of truth: the menus are assigned here in Menu Manager and every other layout
mirrors them through `{THEME_MENUAREA}`.

## Partials

| Slot | File |
| --- | --- |
| header | `headers/header_3columns.html` |
| footer | `footers/footer_3columns.html` |

The header holds the brand and the navigation in a single bar, so it carries
`.main-navbar` and not `.main-header`.
