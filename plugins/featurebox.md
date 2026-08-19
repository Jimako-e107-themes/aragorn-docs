# Featurebox

Aragorn uses the Featurebox plugin to build the marketing sections of the
home page. Each section on the page is one featurebox category, and the
theme supplies the markup through template overrides in

```
THEME/templates/featurebox/featurebox_category_template.php
THEME/templates/featurebox/featurebox_template.php
```

The split between those two files matters when editing them:

| File | Controls |
|---|---|
| `featurebox_category_template.php` | The section as a whole — its caption and the wrapper around the list of items |
| `featurebox_template.php` | The markup of a single item |

Content stays in the database and is edited under **Admin → Featurebox**.
The theme owns the markup, so updating the theme never overwrites the text,
and editing the text never touches the markup.

## Placing a section

Each section is one shortcode in `layouts/home_layout.html`:

```html
{SETSTYLE=section}
{FEATUREBOX|aragorn_features}
```

The modifier after `|` is the category template name, not the category
title. `{SETSTYLE}` selects which section band the theme wraps it in — see
[theme.php](../skeleton/theme-php.md).

{% hint style="info" %}
A category that has no visible items renders nothing at all — no empty band
and no gap in the page. This is why sections are placed through
`{SETSTYLE}` rather than by writing `<section>` markup around the shortcode
in the layout.
{% endhint %}

## One category per template

A category template can be used by exactly one category. Creating a second
category with a template that is already taken fails to save. To repeat a
section pattern with different content, the template has to be copied under
a new name.

This is a limitation of the plugin, reported upstream. Where the repeated
sections are simple, a lighter workaround is to keep several items in one
category and place them individually:

```html
{FEATUREBOX|aragorn_cta=ids=40}
{FEATUREBOX|aragorn_cta=ids=41}
```

## Sections in this section

* **[Features section](featurebox-features.md)** — the three-column feature
  row. Complete.
* **[Hero section](featurebox-hero.md)** — the page-opening hero. Works, but
  see the closing note about its limits.
