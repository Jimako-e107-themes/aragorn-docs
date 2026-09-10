# How this theme was developed

A running log of the Aragorn theme development — the checklist below tracks
progress per area. The reference documentation itself lives in the
systematic sections (Theme structure, Theme layouts, Styling, Standalone
pages, Plugins); each checklist item links to its page.

Progress legend: `██████████` = done, checkbox ticked only at 100%.

## Development areas

* [x] [Theme skeleton](../skeleton/README.md) — `██████████` **100%**
  File structure, `theme.xml` layout registry, `<libraries>` block.
* [ ] [Theme assets](../skeleton/theme-assets.md) — `█████████░` **95%**
  Tabler dist files, icon webfont served locally, legacy class conflicts.
  Icon subsetting still open.
* [x] [theme.html](../skeleton/theme-html.md) — `██████████` **100%**
  Shared wrapper with the `<body>` tag, `{---LAYOUT---}` split marker,
  per-layout `{BODY_CLASS}`, and the two render paths that bypass it
  (`e_IFRAME`, sitedown).
* [ ] [Theme layouts](../layouts/README.md) — `█████████░` **95%**
  Eight layouts incl. the `3columns` default, the magic-shortcode page
  header and the mobile drawers. One `custompages` conflict to resolve.
* [ ] [Header & footer partials](../layouts/header-footer.md) — `█████████░` **90%**
  Two header variants, `.main-header` / `.main-navbar` hooks; footer
  content being finished.
* [ ] [Styling](../styling/README.md) — `████████░░` **85%**
  File boundaries, Tabler variable behaviour, palette structure,
  compatibility shims. A Sass build is still to come.
* [ ] [Signin (header login area)](../plugins/signin.md) — `███████░░░` **70%**
  Core signin plugin templates for the header; Tabler polish remaining.
* [ ] [Auth pages](../standalone/auth-pages.md) — `████████░░` **80%**
  Login / signup / forgot-password on the `auth` layout, members-only
  rendering path solved.
* [ ] [Search page](../standalone/search.md) — `████████░░` **85%**
  Form rebuilt on Bootstrap 5 (core ships no BS5 template), result rows
  styled, `card_header` tablestyle. Pagination remaining.
* [x] [Sitedown page](../standalone/sitedown.md) — `██████████` **100%**
  Maintenance page on the Tabler `empty` component, brand social buttons.
* [x] [News templates](../plugins/news.md) — `█████████░` **90%**
  Main, view, grid and menu templates done with Tabler gallery/job-listing
  patterns; a badge text-contrast bug was found and fixed along the way.
* [x] [Online plugin menus](../plugins/online.md) — `██████████` **100%**
  Last seen and online menus rewritten for Bootstrap 5.
* [ ] [Login menu](../plugins/login-menu.md) — `█████████░` **90%**
  Form and logged-in menu rewritten; two shortcode limits handled in CSS.
* [ ] [Home layout](../layouts/home.md) — `██████░░░░` **60%**
  Front page on `tabler-marketing.css`, two-row header, drawers wired.

## Theme shortcodes

Custom shortcodes defined in `theme_shortcodes.php`
(see [Theme shortcodes](../skeleton/theme-shortcodes.md) for details) — this
list will keep growing as the home and memberdesk layouts develop:

* [x] `{ADVANCED_LOGIN_LINK}` — styled link to the login page
* [x] `{ADVANCED_SIGNUP_LINK}` — styled link to the signup page
* [x] `{ADVANCED_FPW_BUTTON}` — styled forgot-password button
* [x] `{BODY_CLASS}` — per-layout `<body>` classes
* [x] `{PAGE_CLASS}` — per-layout classes for the shared `.page` wrapper
* [x] `{HEADER}` — loads `headers/header_<variant>.html` partial
* [x] `{FOOTER}` — loads `footers/footer_<variant>.html` partial
* [x] `{THEME_TOGGLE}` — dark/light switch
* [x] `{BOTTOM_NAV}` — mobile bottom bar, opens the offcanvas drawers
* [x] `{TILES}` — shortcut tiles under the header
* [x] `{THEME_MENUAREA=n}` — one layout's menu area, rendered on all of them

## Solved along the way

Problems that cost real debugging time — each is documented on its area page
and, where generally useful, extracted into a
[guide](../guides/fixing-news-action-icons.md):

1. **Members-only fpw/signup bypasses the theme layout** (`e_IFRAME` mode) —
   see [Auth pages](../standalone/auth-pages.md).
2. **`simpleParse()` deletes plain `{WORD}` shortcodes** in fpw/membersonly
   template headers — see [Auth pages](../standalone/auth-pages.md).
3. **Dead icons / legacy PNG icons** — missing `BOOTSTRAP` constant and icon
   font — see [the guide](../guides/fixing-news-action-icons.md).
4. **Tabler `.img-responsive` conflicts with e107 core output** — see
   [Compatibility shims](../styling/compat-shims.md).
5. **Double Bootstrap risk** — solved with `files="none"` library
   registration — see [Theme skeleton](../skeleton/README.md).
6. **Menu areas in the header/footer invisible to the Menu Manager** —
   reported upstream in 2019
   ([#3987](https://github.com/e107inc/e107/issues/3987)), closed in 2026:
   the HTML-layout partial pattern makes them fully functional — see
   [the guide](../guides/header-footer-partials.md).
7. **Tabler variables set on a wrapper do nothing** — every component
   re-declares its own, so the value has to go on the component. Five
   confirmed cases — see
   [Working with Tabler variables](../styling/tabler-variables.md).
8. **`.form-control[size]` broke every input group** — an attribute selector
   ties Tabler's own layout rule on specificity and wins on load order — see
   [Compatibility shims](../styling/compat-shims.md).
9. **`e107::link()` silently drops stylesheets** — a CDN `<link>` written
   that way worked by accident and vanished at the next edit — see
   [Theme assets](../skeleton/theme-assets.md).
10. **Plugin menus escape their card** — `list-group` without
    `list-group-flush` — see
    [Overriding a plugin's menu template](../guides/overriding-plugin-templates.md).
11. **`navbar-transparent` cannot be overridden** — `!important` in the
    markup locks a colour decision away from the palette — see
    [Header & footer partials](../layouts/header-footer.md).

## Documentation conventions

Rules for keeping these pages honest. They exist because each was broken at
least once.

### Code snippets are copies, not sources

When a page embeds a file — a layout, a template — it embeds the **whole
file**, comments included. Nothing is trimmed for readability.

The reason is search: someone looking for `panelUser` or `col-sticky` has to
find it here. An abridged snippet costs exactly what the documentation is for.

{% hint style="danger" %}
The file is the source of truth; the snippet is a copy. Re-sync it after
changing the file — never edit the snippet in place.

And take particular care with the comments inside it. They are part of the
code, not commentary on it, so deleting them from the snippet is the same
mistake as deleting them from the file.
{% endhint %}

### Prose moved here is deleted from the code

A long explanation belongs in one place. When it moves into a chapter, the
code keeps a one-line pointer:

```css
/* Tabler reuses .img-responsive as a padding-top aspect helper; neutralize it
   on <img> only. See docs: Compatibility shims. */
```

Enough to know why the rule exists without leaving the file, and no second
copy to drift.

This does **not** apply to code embedded in a snippet — see above. There the
comment is the code.

### Comment the reason, not the rule

A selector or a function signature says what it does. The comment says why it
exists and what would let it be deleted. Rules working around framework
behaviour cite the file and line they work around — those references are what
makes it possible to check, on the next upgrade, whether the workaround is
still needed.

### Say what is unverified

A page may record something that has not been confirmed, as long as it says
so. An open question in the documentation is useful; a confident wrong answer
costs someone an afternoon.

### Archive rather than delete

When a page stops being true — a core bug is fixed, a workaround becomes
unnecessary — it moves to an **Archive** section rather than being deleted or
rewritten.

The page keeps its original text and gains a note at the top: the date, what
changed, and where the current answer is. The value is precisely in recording
how it used to be, for anyone maintaining an older install or wondering why
the code once looked the way it did.

The section will be created when the first page needs it.
