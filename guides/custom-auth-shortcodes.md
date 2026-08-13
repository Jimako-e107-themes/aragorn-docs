# Why the theme defines custom auth shortcodes

The Aragorn auth pages use three custom shortcodes from
`theme_shortcodes.php` instead of the core equivalents:
`{ADVANCED_LOGIN_LINK}`, `{ADVANCED_SIGNUP_LINK}` and
`{ADVANCED_FPW_BUTTON}`. This page records **why** — the core shortcodes
were not wrong, they just could not produce the required output.

## Reason 1 — a link inside a translated sentence

The Tabler auth pages use the *text–link–text* pattern:

> Remember your password? **Sign in** and continue.

Core auth shortcodes output a **bare link or button** — the surrounding
sentence would have to be split into LAN fragments around the shortcode
(`LAN_BEFORE {LINK} LAN_AFTER`), which breaks in languages with different
word order.

The custom shortcodes solve it the e107 way: the **whole sentence is one
LAN string** with a placeholder, and the URL is injected with
`e107::getParser()->lanVars()`:

```php
$text = LAN_THEME_FPW_001;          // full sentence incl. link markup
$text = e107::getParser()->lanVars($text, e_LOGIN);
return $text;
```

Translators translate one natural sentence; the markup stays intact.

## Reason 2 — visibility driven by the `user_reg` preference

The links respect the site's registration settings read once in the
shortcode class constructor: the login/signup links return `null` (render
nothing) when user registration is disabled or does not match the mode.
Core templates would show the links unconditionally.

## Reason 3 — an inline SVG icon inside the submit button

Tabler buttons carry their icon as **inline SVG inside the button label**.
The core `{FPW_SUBMIT}` shortcode renders a plain submit button with a text
label — there is no parameter to inject markup into it. This was the
missing-icon problem on the forgot-password page.

`{ADVANCED_FPW_BUTTON}` builds the button through the core form handler, so
all standard attributes and conventions are kept, and prepends the Tabler
mail SVG:

```php
$fpw_submit = e107::getForm()->button(
    'pwsubmit',
    $icon . $label,                       // inline SVG + LAN label
    'submit',
    '',
    array('class' => 'btn btn-primary btn-4 w-100')
);
```

Note the button **name stays `pwsubmit`** — core `fpw.php` checks
`$_POST['pwsubmit']`, so the custom markup plugs into the unchanged core
processing. No auth logic is duplicated.

## The pattern to reuse

1. Add a `sc_<name>()` method to `theme_shortcodes.php`
   (class extends `e_shortcode`).
2. Put full sentences into the theme LAN file (`languages/English.php`,
   loaded via `e107::lan('theme')`) and inject URLs with `lanVars()`.
3. Build form elements through `e107::getForm()` instead of raw HTML, and
   keep core field names so core processing keeps working.
4. Return `null` when the element should not render — cleaner than empty
   markup.

{% hint style="warning" %}
Theme shortcodes do **not** work in the fpw / membersonly template
`header`/`footer` keys — `simpleParse()` deletes plain `{WORD}` codes
there. Details in
[Auth pages](../development/auth-pages.md).
{% endhint %}
