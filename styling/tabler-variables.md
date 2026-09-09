# Tabler variables

**Status: 100% — five confirmed cases, one rule.**

## The rule

**Set a Tabler variable on the element that declares it, not on an ancestor.**

Tabler declares its component variables *inside the component's own rule
block*. That block re-declares them on every matching element, so a value
inherited from a parent is thrown away the moment the component matches.

```css
/* tabler.css */
.navbar {
  --tblr-navbar-bg: var(--tblr-bg-surface);   /* re-declared here */
  ...
  background: var(--tblr-navbar-bg);
}
```

Set `--tblr-navbar-bg` on a wrapper and every `.navbar` inside it resets to the
surface colour. Set it on the `.navbar` itself and it works.

## The five cases found so far

Each of these cost a round of "I set it and nothing happened".

| Component | Variables | Declared at |
| --- | --- | --- |
| `.navbar` | `--tblr-navbar-bg`, `-color`, `-hover-color`, `-active-color`, `-brand-color` | `tabler.css:4118-4124`, `:14098` |
| `.navbar-nav` | `--tblr-nav-link-hover-bg` | `tabler.css:14823` |
| `.dropdown-menu` | all twenty `--tblr-dropdown-*` | `tabler.css:3597-3620` |
| `.list-group` | `--tblr-list-group-bg`, `-color`, `-border-color`, `-item-padding-*` | `tabler.css:4901-4907` |
| `.btn-secondary` | all `--tblr-btn-*` | `tabler.css:16636-16649` |

The nested case is the one that bites. `header_default.html` puts the
navigation in a `<header class="main-navbar">` that contains a plain
`<div class="navbar">`. Colouring `.main-navbar` alone leaves the inner div
white with dark links, because that div re-declares every navbar variable.
Hence the doubled selector in the brand file:

```css
.main-navbar,
.main-navbar .navbar {
  --tblr-navbar-bg: var(--brand-navbar-bg);
  ...
}
```

## How to check before writing a rule

Search `tabler.css` for the variable name. If it appears with a colon on the
left of a block that also styles the component, it belongs on the component.

```
grep -n "\-\-tblr-dropdown-bg" tabler.css
```

Two declarations — the component's own and yours — is the normal, working
case. Only one, in Tabler, means yours is landing on the wrong element.

## Three more Tabler behaviours worth knowing

### A variable that is declared but never read

`--tblr-accordion-btn-bg` exists, and nothing consumes it: both
`.accordion-button` and `.accordion-header` hardcode
`background-color: transparent`. A coloured accordion header has to set the
background directly, which is why the panel rules in the brand file are the
one place that overrides a Tabler class rather than a variable.

### A default of `inherit`

`--tblr-dropdown-link-color` defaults to `inherit` (`tabler.css:3610`). A
dropdown inside any dark container therefore inherits the container's light
text and renders white on white. This is a theme-wide fragility, not a brand
one — the navigation dropdowns are simply where it was noticed first.

{% hint style="warning" %}
Do not fix this by setting `--tblr-dropdown-link-color` globally. That hides
the problem rather than removing it: every other dropdown in the theme is still
one dark container away from being unreadable, and nobody will find out.
{% endhint %}

### A later rule that beats the variable

`tabler.css:20684` declares `.list-group-item { background-color: inherit }`.
That comes *after* the rule reading `--tblr-list-group-bg` (`:4939`) and has
the same specificity, so the variable never reaches the item — rows take colour
only because they inherit it from `.list-group`.

The brand file therefore sets the panel background on `.accordion-body` and
leaves the list group transparent, rather than relying on which of the two
Tabler rules wins.

## Why variables over overrides at all

Every rule that targets a Tabler class is a rule that can break on a Tabler
upgrade. A variable is a published surface. The brand file uses variables
everywhere it can and name the reason in a comment everywhere they cannot —
that comment is what tells the next reader whether the override is still
needed.
