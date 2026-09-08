# Search page

**Status: 85% — form and result rows done, pagination remaining.**

## What it is

The core search page, `search.php`. It renders two distinct things through
two separate `tablerender()` calls:

* the **search form** — caption `PAGE_NAME SITENAME`, mode `search_head`
* one **result block per search area** — mode `search_result`, called once
  for every plugin that answered the query

With *All Areas* selected the visitor therefore sees several stacked cards,
one per area, each with its own caption.

## Where the template comes from

`search.php` loads the form template on construction:

```php
$this->template = $this->formTemplate(e107::getCoreTemplate('search'));
```

`coreTemplatePath()` resolves the file in this order:

1. `THEME/templates/search_template.php` — the v2 theme override
2. `THEME/search_template.php` — v1 legacy override
3. `e107_core/templates/legacy/search_template.php` — only if `THEME_LEGACY`
4. `e107_core/templates/search_template.php` — the default

{% hint style="warning" %}
**There is no Bootstrap 5 core template for search.** Unlike `fpw`, `signup`
or `user`, the `bootstrap5/` folder contains no `search_template.php`, so
even a theme that declares `BOOTSTRAP 5` falls through to the base file —
which is written for **Bootstrap 3**: `form-horizontal`, `form-group`,
`control-label`, `input-group-btn`, `pull-right`, `<span class="caret">`,
and `col-sm-*` columns with no `.row` parent. Every BS5 theme has to supply
its own override. Reported upstream in the search findings discussion.
{% endhint %}

## The `form` key is replaced, not merged

`getCoreTemplate()` merges the core template with the theme override, but
the merge is `array_merge()` on **top-level keys only**. All seven form
variants live under the single `form` key:

```
form.start   form.category   form.enhanced   form.advanced
form.end     form.message    form.advanced-combo
```

{% hint style="danger" %}
Defining even one sub-key in the theme override replaces the **whole**
`form` array. The override must define all seven, otherwise the missing
parts of the form silently disappear. This is the same "an override replaces
the file entirely" rule as on the [auth pages](auth-pages.md), one level
deeper.
{% endhint %}

## Shortcodes available in the form

The shortcode object is `search.php`'s own class, passed directly to
`parseTemplate()`:

| Shortcode | Renders |
| --- | --- |
| `{SEARCH_FORM_URL}` | form action |
| `{SEARCH_MAIN}` | complete input group — input, submit, advanced dropdown |
| `{SEARCH_MAIN_SEARCHFIELD}` | the bare `<input>` only |
| `{SEARCH_MAIN_SUBMIT}` | submit button plus the hidden `r` field |
| `{SEARCH_ENHANCED}` | the four enhanced fields, one per `form.enhanced` |
| `{ENHANCED_DISPLAY}` | `style="display:none"` unless enhanced is active |
| `{SEARCH_MAIN_CHECKBOXES}` / `{SEARCH_DROPDOWN}` | area selector, depending on the `selector` pref |
| `{SEARCH_ADVANCED}` | per-area advanced block |
| `{SEARCH_MESSAGE}` | stopword notice |

`form.enhanced` and `form.advanced` are **not** parsed for shortcodes — they
go through `simpleParse()`, so only the `{ENHANCED_*}` and `{SEARCH_ADV_*}`
placeholders work there.

## The search field: why not `{SEARCH_MAIN}`

`{SEARCH_MAIN}` emits its own `input-group`, its own `btn-primary` buttons
and a Font Awesome `fa-search` glyph, none of which can be parameterized.
Aragorn builds the input group in the template instead and uses only the
bare field:

```html
<div class="input-group">
    {SEARCH_MAIN_SEARCHFIELD}
    <button class="btn btn-primary" type="submit" name="s" value="1" aria-label="{LAN=199}">
        <i class="ti ti-search" aria-hidden="true"></i>
    </button>
    ...
</div>
<!-- Result offset; SEARCH_MAIN_SEARCHFIELD does not emit it, unlike SEARCH_MAIN -->
<input type="hidden" name="r" value="0" />
```

{% hint style="danger" %}
`{SEARCH_MAIN}` and `{SEARCH_MAIN_SUBMIT}` both append
`<input type="hidden" name="r" value="0">`; `{SEARCH_MAIN_SEARCHFIELD}` does
not. Leave it out and pagination breaks — `r` is the result offset.
{% endhint %}

The dropdown toggle needs no `<span class="caret">`; Bootstrap 5 draws the
arrow with `.dropdown-toggle::after`. Only `data-bs-toggle` is used, since
Aragorn ships `tabler.min.js` (Bootstrap 5.3.7) and has no BS4 alias layer.

## Horizontal rows for the secondary fields

The core template intended `col-sm-3` / `col-sm-9` rows but never wrapped
them in `.row`, so the columns never formed one. Aragorn restores the
intent and swaps the Bootstrap 3 `control-label` for `col-form-label`, which
matches the label's vertical padding to the input height:

```html
<div id="{ENHANCED_DISPLAY_ID}" class="row mb-3">
    <label for="{ENHANCED_DISPLAY_FIELDNAME}" class="col-sm-4 col-form-label">{ENHANCED_TEXT}</label>
    <div class="col-sm-8">
        {ENHANCED_FIELD}
    </div>
</div>
```

`col-sm-4` rather than `col-sm-3` — at 25% the label *Word(s) beginning
with* wraps to two lines inside the main column of the 5-14-5 grid. Below
`sm` the row collapses and the fields stack, which is the wanted mobile
behaviour.

The main search field stays full width: it is the primary control and
should dominate; the horizontal rows visually mark the rest as secondary.

## Solved along the way: `.form-control[size]` broke the input group

The buttons kept wrapping onto a second line below the input. The cause was
a rule in the theme's own `style.css`:

```css
.form-control[size] { width: 100%; }
```

Specificity is `0,2,0` — one class plus one attribute — exactly the same as
Tabler's `.input-group > .form-control { flex: 1 1 auto; width: 1%; min-width: 0 }`.
On a tie the later stylesheet wins, and `style.css` loads last. The input
then claimed the full container width and `flex-wrap: wrap` pushed the
buttons down.

The rule was also **redundant**: Tabler's base `.form-control` already sets
`width: 100%`, and an HTML `size` attribute is only a presentational hint
that any CSS `width` overrides. Deleting it fixed the form.

{% hint style="info" %}
The general lesson: e107 emits `size=` on many inputs, which tempts you to
neutralise it in CSS. Don't — the framework already handles it, and the
attribute selector quietly outranks the framework's own layout rules.
{% endhint %}

## Rendering: the `card_header` style

Both search modes map to one reusable style in `theme.php`:

```php
switch($mode)
{
    // search.php calls tablerender() once per search area, so without a
    // visible caption the visitor cannot tell which area a result set —
    // or a "no matches" notice — belongs to.
    case 'search_result':
    case 'search_head':
        $style = 'card_header';
        break;
}
```

`card_header` renders a Tabler card **with** a `.card-header`, unlike the
default case which prints a bare `.card-body`. It is also reachable from any
layout via `{SETSTYLE=card_header}`.

The caption is assembled by core:

```php
LAN_SEARCH_11 . " " . $res_display . " " . LAN_SEARCH_13 . " " . $qtype
```

giving *Results 1 - 10 of 42 in News*. With no matches `$res_display` is
empty, so it reads *Results in News* — awkward, and with a double space in
the source, but it still identifies the area, which is the point.

## Result rows are CSS-only

The form is fully templatable. The results are not — each row is built as a
fixed string in `search_class.php`, wrapped in a list by `search.php`:

```html
<ul id="search-results-news" class="list-unstyled search-block">
  <li>
    <h4><a class="title visit" href="…">General | How can I use e107?</a></h4>
    <span class="text-muted">Posted on 02 Jan 2009 : 19:43</span>
    <div>…summary with <mark>highlight</mark>…</div>
  </li>
</ul>
```

All three are **direct children** of the `<li>`, which lets the theme target
them precisely without touching anything inside the summary:

```css
.search-block > li + li { … border-top … }
.search-block > li > h4 { … }
.search-block > li > h4 > a.title { … }
.search-block > li > span.text-muted { display: block; … }
```

Two notes on the details:

* Tabler sizes `h4` at `0.875rem` for dashboard use. A result title needs to
  read as the primary element of its row, so the theme raises it to `1rem`.
* The date span is emitted inline and has to be promoted to `display: block`.
* `<mark>` needs nothing — Tabler styles it through `--tblr-highlight-bg` /
  `--tblr-highlight-color` and ships separate dark-mode values.

{% hint style="info" %}
There is no `$this->bullet` to account for: `search_class.php` picks one
from `GLYPH` / `BULLET` / `bullet2.gif` and then unconditionally discards it
two lines later with the comment *"Use CSS instead"*.
{% endhint %}

## Still open

**Pagination.** `search.php` wraps `{NEXTPREV}` in
`<div class="nextprev search form-inline">` — `form-inline` is a Bootstrap 3
/ 4 class that no longer exists — and requests the `default` template prefix
with a hardcoded value. Restyling it means overriding the `default_*` keys
in `THEME/templates/nextprev_template.php`, which changes pagination
**site-wide**, not just on search. Decision pending.

**The navbar search box.** `$SEARCH_TEMPLATE['shortcode']` drives the
`{SEARCH}` shortcode used in the header. It is a separate batch
(`e107_core/shortcodes/batch/search_shortcodes.php`) whose `{SEARCH_INPUT}`
and `{SEARCH_BUTTON}` **do** accept a `class` parm, so Tabler markup is
straightforward there. Not yet written.

## Security notes

* **Never interpolate `$_GET['q']` into the template.** The field value must
  come from `{SEARCH_MAIN_SEARCHFIELD}` (or `{SEARCH_MAIN}`), which runs it
  through `$tp->post_toForm()`. The same applies to the enhanced fields —
  `sc_search_enhanced()` escapes each value itself.
* **`$mode` reaches a `class` attribute.** In `tablestyle()` the mode is
  used as a CSS class on the card. It comes from the `tablerender()` caller,
  which is plugin code rather than user input, but nothing guarantees a
  plugin did not build it from a request, so it is sanitised before use:

  ```php
  $modeClass = preg_replace('/[^a-z0-9_-]/i', '', (string) $mode);
  ```

* **The caption needs no escaping.** It is built from language constants and
  from `search_info[$key]['qtype']`, which each plugin declares in its
  `e_search.php`. The one path that touches `$_GET` uses the request value as
  an **array key** into a plugin-defined list, so no request data reaches the
  output. Escaping it with `toHTML()` would also break plugins that put
  legitimate markup in `qtype`.

## Upstream findings

Working through this page surfaced eight issues in core search, from the
missing Bootstrap 5 template to path constants leaking into result summaries
and a mislabelled `<label for>` in the advanced form. They were written up as
a single report for the e107 maintainers rather than filed as individual
issues.
