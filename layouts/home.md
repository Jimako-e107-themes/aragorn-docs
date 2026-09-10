# Home layout

**Status: 60% — structure and drawers done; content sections in progress.**

## Concept

The front page uses its own `home` layout, mapped via the special `FRONTPAGE`
custompage keyword:

```xml
<layout name='home' title='Home (full width)'>
    <custompages>FRONTPAGE</custompages>
</layout>
```

The layout is built on `css/tabler-marketing.css` (hero components:
`.hero-title`, `.hero-description`, `.section-divider`) rather than the
dashboard chrome.

## Structure

{% hint style="info" %}
This layout is unfinished, so the snippet below is a snapshot rather than a
reference — the section markup and the featurebox categories will change.
Check the file before relying on it.
{% endhint %}

```html
<!-- Mobile drawers for the bottom navigation. This layout has no side columns,
     so the panels exist as drawers only. Block is identical in every layout
     except 3columns - keep the ids and menu areas in sync.
     Menu areas 101-103 are the shared drawer menus; the page sections below
     use 201-204 so the same menu never renders twice on this page. -->
<div class="offcanvas offcanvas-start d-lg-none" tabindex="-1" id="panelLeft">
	<div class="offcanvas-header">
		<h2 class="offcanvas-title h4">Preklady</h2>
		<button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Zavrieť"></button>
	</div>
	<div class="offcanvas-body p-0">
		<div class="accordion" id="accLeft">
			{SETSTYLE=accordion-left}
			{THEME_MENUAREA=101}
		</div>
	</div>
</div>

<div class="offcanvas offcanvas-end d-lg-none" tabindex="-1" id="panelUser">
	<div class="offcanvas-header">
		<h2 class="offcanvas-title h4">Môj účet</h2>
		<button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Zavrieť"></button>
	</div>
	<div class="offcanvas-body p-0">
		<div class="accordion" id="accUser">
			{SETSTYLE=accordion-user}
			{THEME_MENUAREA=102}
		</div>
	</div>
</div>

<div class="offcanvas offcanvas-end d-lg-none" tabindex="-1" id="panelLatest">
	<div class="offcanvas-header">
		<h2 class="offcanvas-title h4">Najnovšie</h2>
		<button type="button" class="btn-close" data-bs-dismiss="offcanvas" aria-label="Zavrieť"></button>
	</div>
	<div class="offcanvas-body p-0">
		<div class="accordion" id="accLatest">
			{SETSTYLE=accordion-latest}
			{THEME_MENUAREA=103}
		</div>
	</div>
</div>

{SETSTYLE=hero}
{FEATUREBOX|aragorn_hero}


<section class="section section-primary mt-4 pt-4">
    <svg class="section-divider" xmlns="http://www.w3.org/2000/svg" viewBox="0 24 150 28" preserveAspectRatio="none">
    <path class="wave-1" d="M-110 44c30 0 58-18 88-18s 58 18 88 18 58-18 88-18 58 18 88 18 v44h-352z"></path>
    <path class="wave-2" d="M-110 44c30 0 58-18 88-18s 58 18 88 18 58-18 88-18 58 18 88 18 v44h-352z"></path>
    <path class="wave-3" d="M-110 44c30 0 58-18 88-18s 58 18 88 18 58-18 88-18 58 18 88 18 v44h-352z"></path>
    </svg>
    <div class="container">
        {SETSTYLE=nocaption}
        {MENU=201}
    </div>
</section>

{SETSTYLE=section}
{FEATUREBOX|aragorn_features}
{MENU=202}


{SETSTYLE=section_alt}

{MENU=203}


{SETSTYLE=section}
{MENU=204}


{ALERTS}
{SETSTYLE=section-white} 
{---}

{SETSTYLE=section-light}
{MENU: path=news/news_grid&caption=Latest news&limit=6&source=latest&featured=0&layout=col-lg-4}
```

## The two-row header

This is the only layout that uses `headers/header_default.html` — brand and
account controls on top, `{NAVIGATION}` on its own row below. It is kept as the
worked example for a site that needs a second header shape, the common case
being a transparent bar over the hero on the front page and an opaque one
everywhere else.

The transparency is a rule in the palette file, not a class in the partial:

```css
.layout-home .main-header {
  background: transparent;
  --tblr-navbar-border-color: transparent;
}
```

`navbar-transparent` would be the obvious class and cannot be used — it sets
`background: transparent !important`, which locks the decision into the markup
where no palette can override it. See
[Header & footer partials](header-footer.md).

## Menu areas

{% hint style="danger" %}
The page sections use areas **201–204**, not 101–104.

The drawers use 101, 102 and 103 like every other layout. Reusing those numbers
for the page body would render the same menus twice on this page — once in the
section and once in the drawer. The offset keeps the two sets apart.
{% endhint %}

| Area | Renders |
|---|---|
| 101, 102, 103 | the three drawers, via `{THEME_MENUAREA}` |
| 201 | section under the hero |
| 202 | after the features featurebox |
| 203, 204 | further content sections |

`{MENU=201}` with a numeric parm is the same as `{MENUAREA=201}` — it calls
`renderArea(201)` (`e107_core/shortcodes/single/menu.php`). The two spellings
are interchangeable; the layout uses the short one.

## Known issues

{% hint style="warning" %}
`{SETSTYLE=section_alt}` and `{SETSTYLE=nocaption}` have no matching case in
`tablestyle()` — the styles there are `section`, `section-light`,
`section-white`, `section-dark`, `section-primary`, and `raw` / `none` /
`caption`. Both fall through to `default` and render as plain cards.

Either the layout means `section-light` and `caption`, or the two cases are
missing from `theme.php`. Not yet decided.
{% endhint %}

## Planned

* Replace the demo hero copy with site prefs / theme shortcodes.
* Decide which sections come from menu areas vs. hardcoded layout markup.
* Real-estate content sections (listings teaser, contact CTA).
