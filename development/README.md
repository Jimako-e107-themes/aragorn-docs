# How this theme was developed

A running log of the Aragorn theme development. Each area below links to a
page describing what was done, which core e107 mechanisms are involved and
which problems had to be solved along the way.

Progress legend: `██████████` = done, checkbox ticked only at 100%.

## Development areas

* [x] [Theme skeleton](theme-skeleton.md) — `██████████` **100%**
  File structure, `theme.xml` layout registry, `<libraries>` block.
* [x] [Theme assets](theme-assets.md) — `██████████` **100%**
  Tabler dist files, icon strategy, `img-responsive` conflict.
* [x] [theme.html](theme-html.md) — `██████████` **100%**
  Shared wrapper with the `<body>` tag, `{---LAYOUT---}` split marker,
  per-layout `{BODY_CLASS}`, and the two render paths that bypass it
  (`e_IFRAME`, sitedown).
* [x] [Theme layouts](theme-layouts.md) — `██████████` **100%**
  All seven layouts finished, incl. the magic-shortcode page header
  (`{---CAPTION---}` / `{---BREADCRUMB---}`) and the sidebar/memberdesk
  menu columns. Page-header markup may later move into `tablestyle()`.
* [ ] [Header & footer partials](header-footer.md) — `████████░░` **85%**
  HTML partials with working `{MENUAREA}` support; footer content being
  finished.
* [ ] [Signin (header login area)](signin.md) — `███████░░░` **70%**
  Core signin plugin templates for the header; Tabler polish remaining.
* [ ] [Auth pages](auth-pages.md) — `████████░░` **80%**
  Login / signup / forgot-password on the `auth` layout, members-only
  rendering path solved.
* [x] [Sitedown page](sitedown.md) — `██████████` **100%**
  Maintenance page on the Tabler `empty` component, brand social buttons.
* [ ] [News templates](news-templates.md) — `██████░░░░` **60%**
  Main + view templates done; grid, menu and carousel pending.
* [ ] [Home layout](home-layout.md) — `█░░░░░░░░░` **10%**
  Front page on `tabler-marketing.css`, just started.

## Theme shortcodes

Custom shortcodes defined in `theme_shortcodes.php`
(see [Theme shortcodes](theme-shortcodes.md) for details) — this list will
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
   see [Auth pages](auth-pages.md).
2. **`simpleParse()` deletes plain `{WORD}` shortcodes** in fpw/membersonly
   template headers — see [Auth pages](auth-pages.md).
3. **Dead icons / legacy PNG icons** — missing `BOOTSTRAP` constant and icon
   font — see [the guide](../guides/fixing-news-action-icons.md).
4. **Tabler `.img-responsive` conflicts with e107 core output** — see
   [Theme assets](theme-assets.md).
5. **Double Bootstrap risk** — solved with `files="none"` library
   registration — see [Theme skeleton](theme-skeleton.md).
6. **Menu areas in the header/footer invisible to the Menu Manager** —
   reported upstream in 2019
   ([#3987](https://github.com/e107inc/e107/issues/3987)), closed in 2026:
   the HTML-layout partial pattern makes them fully functional — see
   [the guide](../guides/header-footer-partials.md).
