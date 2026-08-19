# Hero section

The page-opening hero: a heading with an animated typed phrase, a lead
paragraph and a call-to-action button.

## Setting it up

**Admin → Featurebox → Categories**, create a category with the category
template **Aragorn Hero**. **Items**: add a single item with the item
template **Aragorn - Hero**. More than one item would produce more than one
`<h1>` on the page.

Place it at the top of `layouts/home_layout.html`:

```html
{SETSTYLE=hero}
{FEATUREBOX|aragorn_hero}
```

`{SETSTYLE=hero}` wraps the output in `<header class="hero">` with the
container and row inside it; the category template opens the text column.

## The typed phrase

The rotating phrase after the heading is [Typed.js](https://mattboldt.com/demos/typed-js/).
The library is loaded and initialised by the category template, and its
configuration comes from the category's **Parameters** field:

```
strings: ["more effective", "more efficient", "more productive"], typeSpeed: 100, backSpeed: 50, backDelay: 1000, startDelay: 1000, loop: true, fade: true
```

Write the options **without the surrounding braces** — the plugin adds them.
Any option Typed.js accepts can go here.

The library file belongs at `THEME/js/typed.umd.js`. It is part of the
Tabler distribution, under `dist/libs/typed.js/dist/`.

## Editing hero content

The hero's markup — the `<h1>`, the lead paragraph, the button row — lives
in the item's **Text** field rather than being assembled from separate
fields, because a featurebox item does not have enough fields to hold it.

To make that editable rather than hand-written, the theme ships a **TinyMCE
snippet**. In the editor toolbar, **Insert → Template** offers *Hero body*,
which drops the complete hero markup at the cursor, ready to have its text
replaced.

Snippets live in:

```
THEME/templates/tinymce/snippets/
```

Each is a `.htm` file whose first lines declare how it appears in the
dialog:

```html
<!--
Title: Hero body
Info: Hero heading with typed span, description and two buttons
 -->
<div class="mceTmpl">
	...markup...
</div>
```

The header must be within the first 140 bytes of the file or the snippet is
ignored. The `mceTmpl` wrapper marks what actually gets inserted, keeping
the comment out of the content.

{% hint style="warning" %}
Creating this folder in the theme **replaces** the plugin's own snippets
rather than adding to them. Aragorn therefore ships Tabler-styled
replacements for the two Bootstrap 3 snippets that would otherwise be lost.
{% endhint %}

The editor also loads the theme's own stylesheet, so a snippet looks in the
editor roughly as it will on the page. That requires `css/editor.css`
declared with `scope="wysiwyg"` in `theme.xml`, and **Use theme style**
enabled in the TinyMCE plugin's settings.

## Why this is not a good enough solution

The hero works, and it is admin-editable, but the approach has two real
weaknesses and they are worth stating plainly before anyone builds further
on it.

**A featurebox item does not have the fields a hero needs.** It has a title,
one text field, one image and one link. A hero needs a kicker line, a
heading, a highlighted phrase, a lead paragraph and usually two buttons with
different styles. Everything beyond the first few has to be folded into the
single text field as raw markup. The second button in the Tabler original
had to be dropped entirely, because there is only one link field.

**Structural markup in a WYSIWYG field is fragile.** Once the layout lives
in the text field, a careless edit breaks it — deleting a wrapper `<div>`,
letting the editor drop a class, or pressing Enter in the wrong place is
enough to collapse the columns. The snippet gives a correct starting point;
it does nothing to keep the markup correct afterwards. An editor who only
wants to change a headline is one keystroke away from breaking the section,
with no validation and no warning.

By contrast, the [features section](featurebox-features.md) maps cleanly:
one item is one column, each field is one visible thing, and no editor ever
touches markup. That is what a well-fitted featurebox section looks like, and
the hero is not one.

A better long-term answer is a section with its own fields rather than a
featurebox category — either theme preferences for a single fixed hero, or a
dedicated plugin if it needs to be repeatable. Until then, treat the hero as
working but brittle, and keep a copy of the intended markup so it can be
restored from the snippet when an edit goes wrong.
