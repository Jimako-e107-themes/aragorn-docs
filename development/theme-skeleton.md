# Theme skeleton

**Status: done (100%)**

## File structure

```
e107_themes/aragorn/
├── theme.xml                  theme metadata, layouts, libraries
├── theme.php                  asset registration, tablestyle()
├── theme.html                 shared wrapper with the <body> tag
├── theme_config.php           theme admin preferences
├── theme_shortcodes.php       custom {SHORTCODES}
├── style.css                  theme override stylesheet (loaded last)
├── layouts/                   one HTML file per layout
├── headers/                   shared header partials
├── templates/                 core & plugin template overrides
│   ├── fpw_template.php
│   ├── login_template.php
│   ├── membersonly_template.php
│   ├── navigation_template.php
│   ├── signup_template.php
│   ├── sitedown_template.php
│   ├── news/…
│   ├── signin/…
│   └── social/…
├── css/  js/  images/         theme assets (see Theme assets)
└── languages/English.php
```

## Layout registry (`theme.xml`)

| Layout | Title (admin) | custompages |
|---|---|---|
| `full` | Full page (horizontal header, boxed width) | — |
| `auth` | Splash (auth pages) | `login fpw signup` |
| `page` | Boxed page with page titles (**default**) | `page faq` |
| `home` | Home (full width) | `FRONTPAGE` |
| `memberdesk` | Memberdesk (member area pages) | — |
| `raw` | Raw (output only) | — |

## The `<libraries>` block

```xml
<libraries>
    <library name="bootstrap" version="5" scope="front" files="none" />
    <library name="fontawesome" version="6" scope="front" files="css" />
</libraries>
```

* Registering **bootstrap** makes `theme_handler::loadLibrary()` define the
  `BOOTSTRAP` constant **and** call `e_parse::setBootstrap(5)`. Dozens of core
  code paths branch on these — without them core serves legacy (pre-Bootstrap)
  output. A manual `define()` in `theme.php` is not equivalent, because it
  does not set the parser state.
* `files="none"` loads **no library files**: Tabler bundles the complete
  Bootstrap 5 CSS and JS, and loading the core copies on top would duplicate
  the JS event handlers (dropdowns opening and closing again instantly).
* **fontawesome** registers the local FontAwesome 6 that ships with core, so
  `toGlyph()` / `{GLYPH=...}` output works everywhere.

{% hint style="warning" %}
Library registrations are read on theme (re)activation and the detection is
cached. After changing `<libraries>`: re-save the theme in the Theme Manager
and clear the system cache.
{% endhint %}
