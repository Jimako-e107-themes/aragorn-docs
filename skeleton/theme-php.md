# theme.php

Minimal by design: asset registration and `tablestyle()`. No layout markup
lives here — that is the HTML layout system's job.

## Asset registration

CSS and JS are registered in the theme class constructor; the load order is
documented on [Theme assets](theme-assets.md). No jQuery or Bootstrap
libraries are registered from here — see
[theme.xml](theme-xml.md) for why the `<libraries>` block handles the
constants without loading files.

## `tablestyle()` and the `{SETSTYLE}` map

`tablestyle()` wraps every `tablerender()` output, keyed by mode. Layouts
select the mode with `{SETSTYLE=...}` before their `{---}` / `{MENUAREA}`.

| Style | Used by | Output |
|---|---|---|
| `card` | `full`, `sidebar` aside, `memberdesk` | explicit Tabler card, caption in `.card-header` |
| `default` | `page`, `sidebar` content | designates the **main render** for the magic page header; card without caption (the caption lives in the page header) |
| `login_page`, `fpw`, `signup` | auth pages via core | `card card-md`, caption centered inside the body |
| `menu` | menus without explicit style | Tabler card |
| `raw` / `none` | — | passthrough |

{% hint style="info" %}
A `tablestyle()` refinement pass is planned as its own task — the mapping
above may evolve with it, in particular the page-header handling.
{% endhint %}
