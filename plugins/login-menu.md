# Login menu

**Status: 90% — template rewritten; two shortcode limits handled in CSS.**

## What the theme overrides

```
templates/login_menu/login_menu_template.php
```

covering both states: the login form for guests and the menu for signed-in
users.

{% hint style="danger" %}
`login_menu.php:87` **requires this file instead of the plugin's**, not
alongside it. The plugin file guards each variable with `if(!isset(...))`,
which makes a partial override work in the legacy arrangement — but that file
never runs when the theme's exists.

All seven variables have to be defined here: `$LOGIN_MENU_FORM`,
`$LOGIN_MENU_MESSAGE`, `$LOGIN_MENU_LOGGED`, `$LOGIN_MENU_EXTERNAL_LINK`,
`$LOGIN_MENU_STATS`, `$LM_STATITEM_SEPARATOR`, `$LOGIN_MENU_STATITEM`, plus the
`$sc_style` wrappers.
{% endhint %}

## The signed-in menu

Same problem as every other plugin menu in a panel: `list-group` without
`list-group-flush`, so the rounded first and last items escape the accordion
body.

The override also drops `nav nav-list` from the wrapper. `nav` is a Bootstrap 3
class, and combining it with `list-group` gives two competing sets of padding
rules.

## The login form

Upstream is a centred block separated by `<br />`, with `[ Sign up ]` bracket
wrappers around the links. The override uses `.mb-3` groups, `form-label`, a
full-width button in `.d-grid`, and a plain link stack.

Labels carry `for="username"` and `for="userpass"`, matching the ids the input
shortcodes generate, so they are associated rather than merely positioned
above.

## What the template cannot reach

Two things live in `login_menu_shortcodes.php`, where no template can change
them.

**The grey button.** `sc_lm_loginbutton()` (line 184) hardcodes
`btn btn-default btn-secondary` and accepts no class parm. Handled in the
palette file, on the button itself rather than on an ancestor — `.btn-secondary`
declares all `--tblr-btn-*` variables inside its own block, so a value set on a
wrapper is discarded. See
[Working with Tabler variables](../styling/tabler-variables.md).

**The duplicate label.** `sc_lm_username_input()` (line 105) and
`sc_lm_password_input()` (line 120) emit their own
`<label class='sr-only'>`, which duplicates the visible label the template
renders. Under Bootstrap 5 `sr-only` no longer hides anything — it was renamed
to `visually-hidden` — so the duplicate is visible until the shim in
`style.css` takes effect. See
[Compatibility shims](../styling/compat-shims.md).

## Upstream

Both shortcode limits are worth reporting: a `class` parm on the button, and
either dropping the built-in label or renaming it to `visually-hidden`.
