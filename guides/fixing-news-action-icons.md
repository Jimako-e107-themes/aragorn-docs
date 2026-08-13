# Fixing broken action icons in the News view (e107 v2.4)

## Symptoms

On the news detail page (`news/view-...`), the action buttons below the article are broken in a custom theme:

* the **comment** button renders as an empty grey square (no icon at all),
* the **print** button shows the legacy `eimages/generic/printer.png` bitmap,
* the **edit** button (admins) shows the legacy `eimages/admin_images/edit_16.png` bitmap,
* the buttons carry `btn btn-default btn-secondary` classes that don't match the theme.

Inspecting the empty comment button reveals a dead CSS class:

```html
<i class="glyphicon glyphicon-fa-comment"></i>
```

No such icon exists in any icon font — that's why nothing renders.

## Root cause

There are **two independent causes**, and both live in the theme, not in the news plugin.

### 1. The `BOOTSTRAP` constant is not defined

Core branches on `deftrue('BOOTSTRAP')` in dozens of places. The news action
shortcodes are among them:

* `sc_adminoptions()` (news shortcodes) — glyph when `BOOTSTRAP` is defined, `edit_16.png` when not,
* `emailprint::render_emailprint()` (behind `{PRINTICON}`) — glyph vs. `printer.png`,
* many other core handlers and templates.

If the theme never defines the constant, core silently serves the legacy
(pre-Bootstrap) output everywhere.

### 2. No icon font is registered

Even where core does emit a glyph, it goes through `e_parse::toGlyph()`.
Without a registered FontAwesome library, `toGlyph('fa-comment')` falls back
to the Glyphicons prefix and produces the dead
`glyphicon glyphicon-fa-comment` class — an icon name that exists in no font.

{% hint style="warning" %}
Defining `BOOTSTRAP` manually in `theme.php` is **not** enough. The parser
keeps its own state (`e_parse::setBootstrap()` / `setFontAwesome()`), and that
state is only set when the libraries are registered through `theme.xml`.
{% endhint %}

## The fix

### Step 1 — register the libraries in `theme.xml`

Add a `<libraries>` block to the theme's `theme.xml`:

```xml
<libraries>
    <library name="bootstrap" version="5" scope="front" files="none" />
    <library name="fontawesome" version="6" scope="front" files="css" />
</libraries>
```

What each entry does:

* **bootstrap** — `theme_handler::loadLibrary()` defines `BOOTSTRAP=5` and
  calls `e_parse::setBootstrap(5)`. The `files` attribute controls which
  library assets are loaded: `js`, `css`, or empty for both.
* **fontawesome** — defines `FONTAWESOME=6`, calls `setFontAwesome(6)` and
  loads the local FontAwesome 6 CSS that ships with core
  (`{e_WEB}lib/font-awesome`). From this point `toGlyph()` outputs working
  `fa-*` classes.

{% hint style="info" %}
`files="none"` is intentional when the theme's CSS framework already bundles
Bootstrap (Tabler, most compiled commercial templates do — both CSS **and**
JS). Loading the core Bootstrap library on top of a bundled copy duplicates
the JS event handlers: dropdowns open and immediately close again. With
`files="none"` the constants and parser state are set, but no file is loaded.
{% endhint %}

### Step 2 — re-save the theme and clear the cache

Library registrations are read when the theme is (re)activated, and the
library detection result is cached:

1. Admin → Theme Manager → open the site theme → **Save**.
2. Admin → Tools → Cache → clear the system cache.

Without this step the constants will not appear even with a correct
`theme.xml`.

### Step 3 (optional) — restyle the buttons via a template override

The button *classes* come from the news templates, which pass a `class=`
parameter to each shortcode. To replace the legacy `btn-default` look, copy
the plugin templates into the theme:

```
e107_plugins/news/templates/news_view_template.php
        → THEME/templates/news/news_view_template.php
```

and change the options block, e.g. for a Tabler-based theme:

```php
<div class="options d-print-none hidden-print">
    <div class="btn-actions">{NEWSCOMMENTLINK: class=btn btn-action}{PRINTICON: class=btn btn-action}{ADMINOPTIONS: class=btn btn-action}{SOCIALSHARE}</div>
</div>
```

Notes:

* A theme override **replaces the plugin template file entirely** — every key
  the plugin file defines must exist in the override (including
  `['default']['schema']`), otherwise those parts break silently.
* `{NEWSCOMMENTLINK}` ignores a `glyph=` parameter. Its icon comes from the
  template parameter `['param']['commentlink']` (any HTML, e.g. an inline
  SVG) or defaults to `toGlyph('fa-comment')`.
* `{ADMINOPTIONS}` supports a theme icon hook: if
  `THEME/images/newsedit.png` exists, it is used instead of the glyph.
* `{PRINTICON}` accepts `class=` and renders FontAwesome glyphs once
  `BOOTSTRAP` is defined.

## Verification

After the fix, the comment button should render as:

```html
<i class="fa-solid fa-comment"></i>   <!-- or similar fa-* markup -->
```

and the print/edit buttons should output `fa-print` / `fa-edit` glyphs
instead of `<img>` tags pointing to `eimages/`.

## Takeaways

* Legacy PNG icons and dead `glyphicon glyphicon-fa-*` classes in **any**
  core or plugin output are a theme-level symptom: check the `<libraries>`
  block first.
* `BOOTSTRAP` and `FONTAWESOME` must be set through `theme.xml` so both the
  constants **and** the parser state are configured.
* Use `files="none"` / `files="css"` / `files="js"` to control which library
  assets load — registering a library does not have to mean loading its
  files.
