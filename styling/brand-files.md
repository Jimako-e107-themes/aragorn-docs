# Brand files

**Status: 90% — the contract is settled; a second site would be the real test.**

## What it is

Aragorn separates *structure* from *colour*. Layouts, partials and templates
describe what is on the page; a single brand file describes what colour it is.
Switching sites means swapping one stylesheet.

```php
// theme.php :: css()
e107::css('theme', 'css/tabler.min.css');
e107::css('theme', 'css/tabler-marketing.css');
e107::css('theme', 'css/tabler-themes.min.css');
e107::css('theme', 'css/brand-e107sk.css');   // <- swap this line
e107::css('theme', 'style.css');              // theme overrides - always last
```

The theme ships `css/brand-e107sk.css`: blue `#0099da`, a light header row and
a dark navigation row. A second site is a second file next to it.

## The three layers

Every brand file has the same three sections, in the same order.

**1. Site tokens.** `--brand-*` custom properties. This is the only section that
differs between two sites of the same shape. A new brand starts by copying an
existing file and changing this block.

**2. Tabler mapping.** `--tblr-*` assignments that hand the site tokens to
Tabler. Copy this section verbatim between sites — if a brand needs to change
it, that is a sign a token is missing from section 1.

**3. Components.** The handful of things a variable cannot express. Every rule
here is scoped behind a brand class or one of the theme's own ids, so markup
that has not opted in is untouched.

## The markup contract

A brand file cannot restyle what it cannot select. These hooks are the agreed
handles between the templates and the brand files. **Renaming any of them
breaks every brand.**

| Hook | Where it lives | What it carries |
| --- | --- | --- |
| `.main-header` | the header row with the brand and account controls | header background, link colours |
| `.main-navbar` | the header row with `{NAVIGATION}` | navigation background, rules |
| `#mainNav` | the collapse wrapper around `{NAVIGATION}` | navigation dropdown colours |
| `#accLeft` `#accUser` `#accLatest` | the three side panel accordions | panel and row colours |
| `#searchBar` | the collapsible search bar | — |
| `.login-menu` | the login form wrapper | login button colour |
| `.accordion-brand` | opt-in on any accordion added later | same as the three panel ids |

{% hint style="info" %}
`.main-header` and `.main-navbar` are never both on the same element. They set
competing backgrounds, so on a shared element the winner would come down to
their order in the brand file. `header_3columns.html` holds the navigation in
its single bar and therefore carries `.main-navbar` only.
{% endhint %}

## What may not be in the markup

This is the rule the whole system rests on, and it is narrower than it first
looks. Ordinary Tabler classes are fine in a partial — a brand file loads after
`tabler.min.css` and overrides them by source order. Two things it cannot
override:

| In the markup | Verdict | Why |
| --- | --- | --- |
| `navbar-light`, `sticky-top`, `card`, `accordion` | **may stay** | ordinary classes, overridable |
| `navbar-transparent` | **must go** | `background: transparent !important` (`tabler.css:14814`) |
| `style="background: ..."` | **must go** | an inline style outranks every rule |
| utility classes — `border-bottom`, `bg-*`, `text-*` | **must go** | Bootstrap utilities are `!important` (`tabler.css:7245`) |

A brand that wants what one of those classes did says so itself. The
transparent bar over the home hero, for instance, is a rule in
`brand-e107sk.css` rather than a class in `header_default.html`:

```css
.layout-home .main-header {
  background: transparent;
  --tblr-navbar-border-color: transparent;
}
```

`{LAYOUT_ID}` in `theme.html` expands to `"layout-" + name2id(THEME_LAYOUT)`
(`header_default.php:801`), so every layout has a body class to hang rules on.

## Setting a Tabler variable

Setting a `--tblr-*` variable on an ancestor usually does nothing. This has its
own page — see [Tabler variables](tabler-variables.md) — and it is the single
most common reason a brand rule appears to be ignored.

## Contrast is part of the palette

Both brand files annotate contrast ratios where a value was chosen for
accessibility rather than for looks. This is not decoration in a comment: it is
what stops the next person reusing a decorative colour as a text colour.

The worked example in the shipped file: **`#0099da`** is the identity colour
and reaches only 3.2:1 on white. It is fine as a hero fill or a large display
heading, and unusable behind body text. `--brand-blue-ui` (`#007cb2`, 4.6:1)
therefore exists for everything that carries text, and every rule in section 3
that puts a label on a coloured surface uses it instead.

The pattern generalises: an identity colour picked for a logo rarely doubles as
a text colour. Expect a `-ui` companion token, and name the ratio next to both.

{% hint style="warning" %}
Watch the surface, not just white. `--brand-blue-ui` clears 4.6:1 on white but
only 4.07:1 on the `#f0f0f0` panel background — just under AA. That is why
`brand-e107sk.css` has a separate `--brand-panel-item-link` token.
{% endhint %}

## What does not belong in a brand file

Anything that would be true for every brand:

- compatibility shims for Bootstrap 3/4 classes — see
  [Compatibility shims](compat-shims.md)
- spacing, padding and font sizes that are not brand decisions
- structural fixes, such as making a plugin's list group behave inside a card

Those go in `style.css`. The test: would a second site with a different palette
need this too? If yes, it is not brand.

## Adding a brand

1. Copy the closest existing brand file to `css/brand-<site>.css`.
2. Replace section 1. Note the contrast ratio next to any value chosen for it.
3. Leave section 2 alone unless a token is genuinely missing.
4. Point `theme.php :: css()` at the new file.
5. Walk both header variants, both colour schemes, and one page with side
   panels. Those are where the hooks are.
