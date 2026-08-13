# theme.xml

Theme metadata and registrations — the layout registry is only one part of
this file.

## Metadata

Name, version, author, compatibility (`2.3.2`), summary, keywords.
Re-save the theme in the Theme Manager after any change here — the file is
read on (re)activation, not on every request.

## Layout registry

| Layout | Title (admin) | custompages |
|---|---|---|
| `full` | Full page (horizontal header, boxed width) | — |
| `auth` | Splash (auth pages) | `login fpw signup` |
| `page` | Boxed page with page titles (**default**) | `page faq` |
| `sidebar` | Sidebar (page header, right menu column) | `news` |
| `home` | Home (full width) | `FRONTPAGE` |
| `memberdesk` | Memberdesk (member area pages) | — |
| `raw` | Raw (output only) | — |

`custompages` maps scripts/routes to layouts; `FRONTPAGE` is a special
keyword for the site front page. Details per layout in
[Theme layouts](../layouts/README.md).

## The `<libraries>` block

```xml
<libraries>
    <library name="bootstrap" version="5" scope="front" files="none" />
    <library name="fontawesome" version="6" scope="front" files="css" />
</libraries>
```

* Registering **bootstrap** makes `theme_handler::loadLibrary()` define the
  `BOOTSTRAP` constant **and** call `e_parse::setBootstrap(5)`. Dozens of
  core code paths branch on these — without them core serves legacy
  (pre-Bootstrap) output. A manual `define()` in `theme.php` is not
  equivalent, because it does not set the parser state.
* `files="none"` loads **no library files**: Tabler bundles the complete
  Bootstrap 5 CSS and JS, and loading the core copies on top would
  duplicate the JS event handlers (dropdowns opening and closing again
  instantly).
* **fontawesome** registers the local FontAwesome 6 that ships with core,
  so `toGlyph()` / `{GLYPH=...}` output works everywhere.

{% hint style="warning" %}
Library registrations are read on theme (re)activation and the detection is
cached. After changing `<libraries>`: re-save the theme in the Theme
Manager and clear the system cache.
{% endhint %}
