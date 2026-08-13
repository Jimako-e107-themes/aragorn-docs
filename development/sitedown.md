# Sitedown page

**Status: done (100%)**

## What it is

The maintenance page shown to visitors while the site is closed
(`maintainance_flag` pref). Core redirects guests to `sitedown.php`, which
loads the template from `THEME/templates/sitedown_template.php` (v2.x
location) and parses it with the **full parser** — every shortcode works
here, there is no `simpleParse()` trap like on the fpw path.

{% hint style="info" %}
Main admin can open `sitedown.php` directly and preview the page while the
site is still online.
{% endhint %}

## Design

Based on Tabler's `error-maintenance.html`: `body.border-top-wide
.border-primary` → `.page.page-center` → `.container-tight` → `.empty`
component with `{LOGO: h=80}`, `{SITEDOWN_TABLE_PAGENAME}` as the title and
`{SITEDOWN_TABLE_MAINTAINANCETEXT}` (admin-set HTML) as the subtitle. The
big illustration SVG from the Tabler page was dropped in favour of the site
logo.

## Key implementation points

* **The sitedown page bypasses the theme header pipeline** — no theme CSS
  loads automatically. `{SITEDOWN_THEME_CSS}` covers only the root
  `style.css`, so `css/tabler.min.css` is linked explicitly via the
  `THEME_ABS` PHP constant.
* Replaced the XHTML 1.1 doctype and Bootstrap 3 / FontAwesome 4 **CDN**
  links from the core template with local theme assets.
* Added `viewport` meta (missing upstream — the page shrank on mobile) and
  `noindex` robots meta.
* The maintenance text sits in a `<div>`, not `<p>` — the pref content
  contains its own block HTML.
* Root `style.css` is loaded last; it carries the `img.img-responsive` fix
  that the `{LOGO}` output needs under Tabler CSS.

## Social icons — the `aragorn` template variant

`{XURL_ICONS}` supports template variants
(`e107_plugins/social/templates/social_xurl_template.php`, overridable at
`THEME/templates/social/social_xurl_template.php`). The theme adds an
`aragorn` variant rendering Tabler **brand buttons** — the color comes from
Tabler's `btn-facebook`, `btn-instagram`, ... classes, the icon is the core
inline SVG delivered by the `{XURL_ICONS_SVG}` shortcode
(`e107_plugins/social/svg/<network>.svg`):

```
{XURL_ICONS: template=aragorn&type=facebook,twitter,instagram,youtube,flickr,vimeo,github,linkedin}
```

This removed two stylesheet dependencies from the page (FontAwesome and the
social plugin CSS) and the same call can be reused in the site footer. The
upstream `default` variant is kept in the override file, because a theme
override replaces the plugin template file entirely.
