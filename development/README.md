# How this theme was developed

A running log of the Aragorn theme development — the checklist below tracks
progress per area. The reference documentation itself lives in the
systematic sections (Theme skeleton, Theme layouts, Standalone pages,
Plugins); each checklist item links to its page.

Progress legend: `██████████` = done, checkbox ticked only at 100%.

## Development areas

* [x] [Theme skeleton](../skeleton/README.md) — `██████████` **100%**
  File structure, `theme.xml` layout registry, `<libraries>` block.
* [x] [Theme assets](../skeleton/theme-assets.md) — `██████████` **100%**
  Tabler dist files, icon strategy, `img-responsive` conflict.
* [x] [theme.html](../skeleton/theme-html.md) — `██████████` **100%**
  Shared wrapper with the `<body>` tag, `{---LAYOUT---}` split marker,
  per-layout `{BODY_CLASS}`, and the two render paths that bypass it
  (`e_IFRAME`, sitedown).
* [x] [Theme layouts](../layouts/README.md) — `██████████` **100%**
  All seven layouts finished, incl. the magic-shortcode page header
  (`{---CAPTION---}` / `{---BREADCRUMB---}`) and the sidebar/memberdesk
  menu columns. Page-header markup may later move into `tablestyle()`.
* [ ] [Header & footer partials](../layouts/header-footer.md) — `████████░░` **85%**
  HTML partials with working `{MENUAREA}` support; footer content being
  finished.
* [ ] [Signin (header login area)](../plugins/signin.md) — `███████░░░` **70%**
  Core signin plugin templates for the header; Tabler polish remaining.
* [ ] [Auth pages](../standalone/auth-pages.md) — `████████░░` **80%**
  Login / signup / forgot-password on the `auth` layout, members-only
  rendering path solved.
* [x] [Sitedown page](../standalone/sitedown.md) — `██████████` **100%**
  Maintenance page on the Tabler `empty` component, brand social buttons.
* [x] [News templates](../plugins/news.md) — `████████░░` **90%**
  Main, view, grid and menu templates done with Tabler gallery/job-listing
  patterns; a badge text-contrast bug was found and fixed along the way.
* [ ] [Home layout](../layouts/home.md) — `█░░░░░░░░░` **10%**
  Front page on `tabler-marketing.css`, just started.

## Theme shortcodes

Custom shortcodes defined in `theme_shortcodes.php`
(see [Theme shortcodes](../skeleton/theme-shortcodes.md) for details) — this list will
keep growing as the home and memberdesk layouts develop:

* [x] `{ADVANCED_LOGIN_LINK}` — styled link to the login page
* [x] `{ADVANCED_SIGNUP_LINK}` — styled link to the signup page
* [x] `{ADVANCED_FPW_BUTTON}` — styled forgot-password button
* [x] `{BODY_CLASS}` — per-layout `<body>` classes
* [x] `{PAGE_CLASS}` — per-layout classes for the shared `.page` wrapper
* [x] `{HEADER}` — loads `headers/header_<variant>.html` partial
* [x] `{FOOTER}` — loads `footers/footer_<variant>.html` partial

## Solved along the way

Problems that cost real debugging time — each is documented on its area page
and, where generally useful, extracted into a [guide](../guides/fixing-news-action-icons.md):

1. **Members-only fpw/signup bypasses the theme layout** (`e_IFRAME` mode) —
   see [Auth pages](../standalone/auth-pages.md).
2. **`simpleParse()` deletes plain `{WORD}` shortcodes** in fpw/membersonly
   template headers — see [Auth pages](../standalone/auth-pages.md).
3. **Dead icons / legacy PNG icons** — missing `BOOTSTRAP` constant and icon
   font — see [the guide](../guides/fixing-news-action-icons.md).
4. **Tabler `.img-responsive` conflicts with e107 core output** — see
   [Theme assets](../skeleton/theme-assets.md).
5. **Double Bootstrap risk** — solved with `files="none"` library
   registration — see [Theme skeleton](../skeleton/README.md).
6. **Menu areas in the header/footer invisible to the Menu Manager** —
   reported upstream in 2019
   ([#3987](https://github.com/e107inc/e107/issues/3987)), closed in 2026:
   the HTML-layout partial pattern makes them fully functional — see
   [the guide](../guides/header-footer-partials.md).
