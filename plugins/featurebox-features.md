# Features section

The three-column feature row: an icon in a rounded shape, a heading and a
short paragraph, repeated across the width of the page.

## Setting it up

**Admin → Featurebox → Categories**, create a category with the category
template **Aragorn Features**. The category title becomes the section
heading.

**Admin → Featurebox → Items**, add one item per column with the item
template **Aragorn - Feature column**. Three items is the intended count;
the row reflows to two columns on medium screens and one on small.

Per item:

| Field | Used as |
|---|---|
| Title | Column heading |
| Text | Column paragraph |
| Image | The icon inside the rounded shape |
| Link | Not used by this template |

Then place the section in `layouts/home_layout.html`:

```html
{SETSTYLE=section}
{FEATUREBOX|aragorn_features}
```

## Section subtitle

Featurebox categories have a title but no subtitle field, so the line under
the section heading is a theme preference instead: **Admin → Theme →
Aragorn → Configure → Features: subtitle**. It is multilingual — each
installed language stores its own value.

The category template pulls it in through the theme's own `{THEME_PREF}`
shortcode:

```php
$FEATUREBOX_CATEGORY_TEMPLATE['aragorn_features']['caption'] =
	'{FEATUREBOX_CATEGORY_TITLE} <div class="section-description">{THEME_PREF: name=features_subtitle&default=What we offer}</div>';
```

The `default=` value is what shows before anything has been saved, so the
section never renders a bare heading with an empty line beneath it. New
theme preferences are declared in `theme_config.php`; see
[theme_config.php](../skeleton/theme-config.md).

{% hint style="info" %}
Section chrome — headings, subtitles, standing intro lines — belongs in
theme preferences. Featurebox holds the repeatable content: the columns
themselves. Keeping the two apart is why the subtitle is not simply another
featurebox item.
{% endhint %}

## Icon images

{% hint style="warning" %}
Upload icons at the size they should appear. The image inside the rounded
shape is **not** resized by Tabler.

Tabler sizes shape icons with a single rule that requires the `icon` class:

```css
.shape .icon { width: var(--tblr-shape-icon-size); height: var(--tblr-shape-icon-size); }
```

Featurebox renders its image as `<img class="featurebox img-fluid">`, with
no `icon` class, so that rule never applies to it. The theme adds its own
rule in `style.css` to constrain it, but an oversized source file still
costs page weight, and a file with a lot of empty margin will look small
inside the shape however it is scaled.

Square SVG or PNG at roughly 48×48, with the artwork filling the frame,
gives the intended result.
{% endhint %}

## Changing the shape size

The rounded shape and the icon inside it are sized by two Tabler custom
properties. They cannot be overridden from `:root`, because Tabler declares
them on the element itself and a declaration on the element always wins
over an inherited value — `!important` does not change this. Override them
on the same selector instead:

```css
.shape-md {
	--tblr-shape-size: 4rem;
	--tblr-shape-icon-size: 2.5rem;
}
```

Often simpler is to change the class in the item template to one of
Tabler's larger presets — `shape-lg`, `shape-xl` or `shape-2xl` — and leave
the variables alone.
