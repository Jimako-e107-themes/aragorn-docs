# Theme shortcodes

Custom shortcodes live in `theme_shortcodes.php` (class extending
`e_shortcode`). They are available in all theme layouts and templates parsed
by the full parser.

{% hint style="danger" %}
Theme shortcodes do **not** work in the fpw / membersonly template
`header` / `footer` keys — `simpleParse()` deletes every plain `{WORD}`
code there before the full parser runs. See
[Auth pages](auth-pages.md) for the workaround (PHP constants +
`e107::getThemePref()`).
{% endhint %}

## Implemented

| Shortcode | Purpose | Status |
|---|---|---|
| `{ADVANCED_LOGIN_LINK}` | styled link to the login page | done |
| `{ADVANCED_SIGNUP_LINK}` | styled link to the signup page | done |
| `{ADVANCED_FPW_BUTTON}` | styled forgot-password button | done |
| `{BODY_CLASS}` | per-layout `<body>` classes | done |
| `{PAGE_CLASS}` | per-layout classes for the `.page` wrapper in theme.html | done |
| `{HEADER}` | loads `headers/header_<variant>.html` partial | done |
| `{FOOTER}` | loads `footers/footer_<variant>.html` partial | done |

Why the three `ADVANCED_*` shortcodes exist instead of the core equivalents
(link-in-sentence LANs, `user_reg` visibility, inline SVG in the submit
button) is documented in
[Why the theme defines custom auth shortcodes](../guides/custom-auth-shortcodes.md).

## Planned

The list below is a snapshot — more shortcodes will be added as the home
and memberdesk layouts develop.

* `{THEME_AUTH_BGIMAGE}` — auth splash image from theme preferences
  (layout-side counterpart to the PHP pref read used in the fpw template).
* Content helpers for the home layout sections.
* Memberdesk helpers for the custom plugins in preparation.
