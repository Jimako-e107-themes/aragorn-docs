# Auth pages

**Status: 80% — login / fpw / members-only solved, signup polish remaining.**

## What they are

The three core authentication pages — `login.php`, `signup.php`, `fpw.php`
(forgot password) — mapped to the `auth` layout (formerly named `splash`)
via `theme.xml`:

```xml
<layout name='auth' title='Splash (auth pages)'>
    <custompages>login fpw signup</custompages>
</layout>
```

The layout is the Tabler auth pattern: `.page.page-center` →
`.container-tight` → centered brand → `{---}`. The card around the form
itself comes from `tablestyle()`, **not** from the layout — that is what
keeps the standard and members-only render paths visually identical.

## The famous problem: members-only renders differently

This was a long-standing mystery. With *Members only* enabled, fpw and
signup ignore the theme layout completely. Root cause (core):

1. `fpw.php` / `signup.php` call `define('e_IFRAME', true)` in their
   members-only branch and pass the template `header`/`footer` as
   `e_IFRAME_HEADER` / `e_IFRAME_FOOTER`.
2. In `header_default.php`, `e_IFRAME` mode **discards** the theme's
   `$HEADER`/`$FOOTER` and prints the iframe strings instead — layout,
   custompages mapping, `theme.html`, everything is bypassed.
3. Bonus core quirk: with an HTML-layout theme the page then has **no
   `<body>` tag at all** (the layout that carried it was discarded and the
   `noBody` code path prints none).

`login.php` is different again: it renders in `e_IFRAME` mode **always**
(not only members-only), but offers an official opt-out.

## The solutions

### Login — official opt-out

In the theme override `templates/login_template.php`:

```php
$LOGIN_TEMPLATE['page']['noiframe'] = 1;
```

Login then renders through the full theme (auth layout).

### fpw / signup — replicate the layout in the template

No opt-out exists, so the theme override `templates/fpw_template.php`
defines `header`/`footer` that replicate the auth layout wrapper. Those two
keys are used **only** in the members-only (`e_IFRAME`) path — the standard
path uses only the `form` key — so nothing is duplicated at runtime.
`membersonly_template.php` does the same for signup via its
`['signup']['header']` / `['footer']` keys.

{% hint style="danger" %}
**The `simpleParse()` trap.** `fpw.php` pipes the template header/footer
through `simpleParse()`, whose regex `{[\w]+}` **deletes every plain
`{WORD}` shortcode** before the full parser ever runs — `{SITENAME}`,
`{THEME}`, custom theme shortcodes, all gone. Only parameterized shortcodes
survive (e.g. `{SITELOGO: h=40}` — the colon breaks the match). Rule:
build paths and texts in these headers/footers from **PHP constants**
(`SITEURL`, `SITENAME`, `SITETAG`, `THEME_ABS`) and read theme prefs via
`e107::getThemePref()` in PHP. Documented as a code comment in the template.
{% endhint %}

{% hint style="warning" %}
A theme template override **replaces the core file entirely** — the fpw
override must define the `form` key too, otherwise the standard page renders
an empty form.
{% endhint %}

## Security notes

* Theme pref values used in HTML attributes (auth image path) are escaped
  with `e107::getParser()->toAttribute()`.
* The templates contain no custom `<form>` actions and no auth logic —
  authentication stays entirely in core.
* During this work the fork's `fpw.php` was found lagging behind upstream
  security fixes (reset-code replay, `hash_equals()` comparison, expired-code
  fallthrough) — synced before building templates on top of it.

## Remaining work (the missing 20%)

* Signup form field styling pass (Tabler `mb-3` / `form-label` markup).
* Auth splash image as a theme preference (`{THEME_AUTH_BGIMAGE}` shortcode
  for layouts, PHP pref read for the fpw/membersonly templates).
* Report the missing-`<body>`-tag `e_IFRAME` quirk upstream; consider a
  `noiframe` PR for `fpw.php`/`signup.php` mirroring `login.php`.
