# Full layout

**Status: done (100%)**

Full page with the horizontal header and boxed content width. Registered
without `custompages` — assigned manually where needed.

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
	<div class="page-body">
		<div class="container-xxl">
			{SETSTYLE=card}
			{---}
		</div>
	</div>
</div>
```

* Starts at `.page-wrapper` — the `.page` wrapper and the header come from
  [theme.html](../skeleton/theme-html.md) + the
  [header partial](header-footer.md), which for this layout is
  `headers/header_3columns.html`.
* Content renders as Tabler cards via `{SETSTYLE=card}`.

## Drawers

This layout has no side columns, so the three panels exist as **drawers only** —
`offcanvas … d-lg-none`, opened from `{BOTTOM_NAV}`. The block sits above
`.page-wrapper` and is identical in every layout that has it.

Menu areas 101, 102 and 103, addressed with `{THEME_MENUAREA}` — the menus are
assigned on the `3columns` layout and mirrored here. See
[Shared menu areas](../guides/shared-menu-areas.md).
