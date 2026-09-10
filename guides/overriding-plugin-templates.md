# Overriding a plugin's menu template from a theme

Plugin menus render inside whatever the theme wraps them in. When the plugin
ships Bootstrap 3 or 4 markup and the theme is Bootstrap 5, the result is a
menu that looks broken in a way no stylesheet can fully repair — so the theme
overrides the template.

This guide covers the mechanics, which are the same for every plugin and are
**not** the same as for core templates. Aragorn's own overrides are documented
per plugin under Plugins.

## The usual symptom

A list group whose rounded corners and border escape the card it sits in, while
another menu in the same panel sits flush.

```html
<!-- correct inside a card -->
<div class="list-group list-group-flush">

<!-- plugin default -->
<ul class="list-group lastseen-menu">
```

`list-group-flush` is what tells Bootstrap the list is inside a container: it
drops the outer border and the rounded first and last items. Without it the
list keeps its own frame.

The same rewrite usually has to replace a set of dead class names as well —
`muted`, `pull-right`, `float-right`, `text-right`, `mr-3`, the `media` family,
`label label-primary`. What each became is on
[Compatibility shims](../styling/compat-shims.md).

## Where the theme file goes

```
THEME/templates/<plugin>/<plugin>_menu_template.php
```

resolved by `e107::templatePath()` (`e107_class.php:3745`). Some older menus
also check a **v1 legacy path** first — `THEME/<plugin>_menu_template.php`, in
the theme root, no subdirectory. A stray file there silently wins over the v2
one, which is worth knowing before spending an hour on why an override is
ignored.

## Plugin templates are not merged

{% hint style="danger" %}
Core templates merge the theme's file over the plugin's. Plugin templates do
not. `e107::getTemplate()` returns early (`e107_class.php:3867`):

```php
if($merge === false || $override === false) { return $ret; }
```

and `$merge` defaults to `false`. Whatever the theme file does not define is
absent — there is no inheritance to fall back on.
{% endhint %}

So an override is all-or-nothing per top-level key. Copy the plugin's file as
the starting point and edit it; do not write only the parts you want to change.

## Some plugins replace the file outright

A second pattern exists, and it is stricter still. Older menus require one file
*or* the other:

```php
if(is_readable(THEME.'templates/<plugin>/<plugin>_template.php'))
{
	require(THEME.'templates/<plugin>/<plugin>_template.php');
}
else
{
	require(e_PLUGIN.'<plugin>/<plugin>_template.php');
}
```

The plugin file typically guards each variable with `if(!isset(...))`, which
makes a partial override work in the *legacy* arrangement — but that file never
runs at all when the theme's exists. Every variable it defines has to be
defined in the theme file, including the `$sc_style` wrappers.

Check the plugin's menu PHP before writing the override. The two patterns look
identical from the outside and fail in different ways.

## Watch for a second variable

Some templates carry more than one top-level variable — a
`$<PLUGIN>_MENU_TEMPLATE` and a `$<PLUGIN>_MENU_WRAPPER`, for instance.
`_getTemplate()` reads only the one named after the template id, so wrappers
defined in a theme file may never be read while the ones from the plugin still
apply.

If half the rewrite takes effect and half does not, this is the first thing to
check.

## Language strings

Use the `{LAN=SOMETHING}` shortcode form, not the `LAN_SOMETHING` constant.

A theme template is read before the plugin's language file is guaranteed to be
loaded, and an undefined constant is a **fatal error in PHP 8**, not a notice.
Upstream files are often inconsistent about this — some blocks use the
shortcode, others concatenate the constant. A theme override should use the
shortcode throughout.

## What an override still cannot reach

Markup generated in the plugin's shortcodes rather than its template. If a
button's classes are hardcoded in `sc_*()`, no template can change them, and
the choice is a scoped CSS rule or an upstream patch. Note which it is in a
comment — otherwise the next person tries the template again.
