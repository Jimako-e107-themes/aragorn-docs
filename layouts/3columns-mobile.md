# 3columns on mobile

**Status: 90% — pattern settled and shipped across all layouts.**

How a three-column desktop page becomes usable on a phone, and why each piece
is what it is. The desktop side of the layout is on
[3columns](3columns.md).

## The problem

A catalogue column, content, and an account column. On a phone they stack, and
stacking is the wrong answer: the visitor gets two columns of navigation to
scroll past before reaching a single article, and another one after it.

Hiding them is not the answer either — those columns are how the site is
navigated.

## The shape

Three moving parts, in this order:

1. The side columns become **offcanvas drawers** — off-screen until asked for.
2. A **fixed bottom bar** provides the way to ask.
3. Inside each drawer, the menus are **accordions** rather than a flat list.

Each is a separate decision, and each was made for a different reason.

## Why drawers, not stacking

`offcanvas-lg` is the one Tabler component that is a column above a breakpoint
and a drawer below it, from the same markup:

```html
<div class="offcanvas-lg offcanvas-end col-sticky" tabindex="-1" id="panelUser">
```

Above `lg` it is an ordinary block in the grid. Below it, it leaves the flow
and waits for a trigger.

That matters more than it sounds: **one markup, one set of menu areas, one
Menu Manager configuration**. A separate mobile navigation would mean a second
set of everything, kept in sync by hand.

On layouts with no side columns — `full`, `page`, `home` — the same panels are
plain `offcanvas … d-lg-none`: drawers always, columns never. The ids and the
menu areas stay identical, which is what lets the bottom bar be the same
everywhere.

## Why a bottom bar, not a hamburger

The header already has a hamburger, and it belongs to `{NAVIGATION}` — the
site's main menu. The drawers are something else: catalogue, account,
activity. Putting four unrelated things behind one button makes the visitor
open it to find out what is inside.

Reachability is the other half. On a phone held one-handed, the bottom edge is
where the thumb is; the top corners are the hardest part of the screen to
reach. Navigation used on every page belongs where the hand already is.

The bar carries four targets — home, and one per drawer. It is
[`{BOTTOM_NAV}`](../skeleton/shortcodes/bottom-nav.md), rendered from
`theme.html` and `d-lg-none`.

{% hint style="danger" %}
The bar and the panels are a pair. A layout that renders one without the other
produces `Cannot read properties of undefined (reading 'backdrop')` — Bootstrap
returns early on a missing offcanvas target, leaving the config undefined
before the backdrop is read.
{% endhint %}

## Why three areas, not one

The drawers could have been a single panel with everything in it. They are
three because they answer three different questions, and a visitor opening one
knows which:

| Drawer | Area | Answers |
|---|---|---|
| `#panelLeft` | 101 | what is on this site — catalogue, categories |
| `#panelUser` | 102 | my account — login, settings, profile |
| `#panelLatest` | 103 | what is new — latest comments, last seen |

The split also matches the desktop layout, so nothing is reordered as the
viewport changes. What is on the left at 1400px is behind the left-hand
trigger at 400px.

## Why accordions inside them

A drawer is a narrow, tall, scrolling surface. Four or five menus in it become
a scroll with no landmarks.

Accordions give each menu a persistent header, so the drawer opens as a short
list of labelled sections rather than a wall of links. Tabler's
`data-bs-parent` keeps one section open at a time, which is what stops the
drawer growing back into that wall.

The rendering is `tablestyle()`'s `accordion-left`, `accordion-user` and
`accordion-latest` styles, which delegate to `accordionItem()` — one accordion
item per menu. The `.accordion` wrapper and its id live in the layout, because
`data-bs-parent` needs an id the theme cannot invent per menu.

The first item in **each** panel opens by default: items are counted per
parent, not per page, so all three drawers open showing something.

{% hint style="info" %}
A menu with no caption has nothing to click on, so it degrades to a plain card
rather than producing a headerless accordion item.
{% endhint %}

## Why the 24-column grid

This one is desktop-side, but it is why the mobile pattern is needed at all.

Twelve columns give two symmetric three-column splits: 3-6-3 (25 / 50 / 25),
where the side columns are too wide, and 2-8-2, where they are too narrow. The
ratio that works is 5-14-5 of twenty-four — **20.83 / 58.33 / 20.83**.

Three classes in `style.css` cover it, and they are *additional* classes rather
than a redefinition of Bootstrap's grid — see [3columns](3columns.md) for why
recompiling at 24 columns was rejected.

Below `lg` none of it applies: Bootstrap's `.row > *` already sets
`width: 100%`, so the columns stack on their own and the drawers take over.

## Menu Manager

The drawers are visible only below `lg`, and Menu Manager renders the front end
in a desktop-width iframe — so their drop zones would be invisible and the
areas unusable.

`style.css` flattens them there, scoped to the manager's own iframe:

```css
#menu_iframe .offcanvas {
	position: static;
	visibility: visible;
	transform: none;
	display: block;
}
```

Only `3columns` shows those areas as drop zones at all. Every other layout
mirrors its assignment through
[`{THEME_MENUAREA}`](../skeleton/shortcodes/theme-menuarea.md), so there is one
place to configure and no drift.

## Open

* The bottom bar's four items are hardcoded. Whether they should be
  configurable is undecided.
* Suppression is a blacklist of layouts. A whitelist of layouts that carry the
  panels would fail safe instead.
