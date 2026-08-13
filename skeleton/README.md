# Theme skeleton

**Status: done (100%)**

The base files every e107 theme is built from, and how Aragorn fills them.

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

## Pages in this section

* [theme.xml](theme-xml.md) — metadata, layout registry, the `<libraries>` block
* [theme.html](theme-html.md) — shared wrapper, `<body>`, `.page`, split markers
* [theme.php](theme-php.md) — asset registration and `tablestyle()`
* [theme_shortcodes.php](theme-shortcodes.md) — custom theme shortcodes
* [theme_config.php](theme-config.md) — theme admin preferences (skeleton)
* [Theme assets](theme-assets.md) — Tabler files, icon strategy, CSS conflicts
