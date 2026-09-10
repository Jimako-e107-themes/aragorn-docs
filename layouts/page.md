# Page layout

**Status: done (100%)**

The layout for standalone pages: boxed page with the Tabler page header filled
by the magic shortcodes.

{% hint style="warning" %}
`page` and `3columns` both declare `page faq` in `custompages`. Two layouts
claiming the same pages is unresolved — see [Theme layouts](README.md).
{% endhint %}

The `.page` wrapper and the header come from
[theme.html](../skeleton/theme-html.md) + `headers/header_3columns.html`; this
file starts at `.page-wrapper`.

The page header is filled by the magic shortcodes `{---CAPTION---}` and
`{---BREADCRUMB---}`, which are populated from the **main render** — what
`{SETSTYLE=default}` designates.

## Structure

```html
<!-- Mobile drawers for the bottom navigation. This layout has no side columns,
     so the panels exist as drawers only. Block is identical in every layout
     except 3columns - keep the ids and menu areas in sync. -->
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

<div class="page-wrapper">
	<div class="page-header d-print-none" aria-label="Page header">
		<div class="container-xxl">
			<div class="row g-2 align-items-center">
				<div class="col">
					<h2 class="page-title">{---CAPTION---}</h2>
				</div>
				<div class="col-auto ms-auto d-print-none">
					<div class="d-flex">
						{---BREADCRUMB---}
					</div>
				</div>
			</div>
		</div>
	</div>
	<div class="page-body">
		<div class="container-xxl">
			{SETSTYLE=default}
			{---}
		</div>
	</div>
</div>
```

{% hint style="danger" %}
Never switch this layout to `{SETSTYLE=card}` — the magic shortcodes are fed by
the `default`-styled main render, and with `card` the page header stays empty.
See the [magic shortcodes section](README.md).
{% endhint %}

## Drawers

No side columns, so the three panels are drawers only (`d-lg-none`), on menu
areas 101–103 via `{THEME_MENUAREA}`. See
[Shared menu areas](../guides/shared-menu-areas.md).

The page-header markup may later move into `tablestyle()` if a suitable
template mechanism is found.
