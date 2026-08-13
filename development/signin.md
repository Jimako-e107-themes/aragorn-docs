# Signin (header login area)

**Status: 70% — functional Bootstrap 5 markup, Tabler polish remaining.**

## What it is

The core **signin plugin** renders the login/user area in the page header —
the `{SIGNIN}` shortcode placed in the header partial. It has two states,
each with its own template key:

* `signin` — shown to guests: a signup link plus a dropdown containing the
  full login form (username, password, optional image code, *remember me*,
  forgot-password and resend-activation links).
* `signout` — shown to logged-in members: avatar dropdown with settings,
  profile, admin link (for admins) and the PM navigation hook.

{% hint style="info" %}
The dropdown login form posts to core signin processing — the theme
template only supplies markup via `{SIGNIN_FORM=start}` /
`{SIGNIN_INPUT_*}` / `{SIGNIN_FORM=end}` shortcodes. No auth logic lives in
the theme.
{% endhint %}

## Override location

```
THEME/templates/signin/signin_template.php
```

Defines `$SIGNIN_TEMPLATE['signin']`, `$SIGNIN_TEMPLATE['signout']` and the
matching `$SIGNIN_WRAPPER` entries (signup link, admin link, PM nav item).
As with every template override, the file replaces the plugin's template
entirely — keep all keys present.

## Current state

Bootstrap 5 navbar-dropdown markup, working in the Tabler header:
`navbar-nav` + `nav-item dropdown`, `data-bs-toggle` (with legacy
`data-toggle` kept for BC), `d-grid` button stack, `{USER_AVATAR}` with
circle crop in the member state. FontAwesome icons render correctly thanks
to the registered `fontawesome` library (see
[Theme skeleton](theme-skeleton.md)).

## Remaining polish (the missing 30%)

* Replace Bootstrap 3 relics: `divider-vertical`, `btn-block`
  (→ `d-grid`/`w-100`), duplicated `data-toggle` attributes once BC is not
  needed.
* Move inline `style=""` attributes (dropdown min-width/padding) into
  `style.css`.
* Swap FontAwesome font icons for Tabler inline SVG where it improves
  consistency with the rest of the header.
* Align dropdown card styling with Tabler `dropdown-menu` conventions
  (`dropdown-menu-arrow`, `dropdown-item` spacing).
