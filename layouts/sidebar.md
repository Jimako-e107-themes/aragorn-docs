# Sidebar layout

**Status: 95% — right column aligned with 3columns; breakpoints to confirm.**

Magic page header + content with a menu column on the **right**
(`custompages: news`). Pattern: Tabler `job-listing.html`.

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
			<div class="row g-4">
				<div class="col-md-9">
					{SETSTYLE=default}
					{---}
				</div>
				<!-- col-sticky, not sticky-top: sticky-top pins to top:0 and the
				     sidebar would slide under the sticky header. -->
		<div class="col-lg-3">
			<div class="col-sticky">
		
				<!-- REGION u - account -->
				<div class="offcanvas-lg offcanvas-end" tabindex="-1" id="panelUser">
					<div class="offcanvas-header d-lg-none">
						<h2 class="offcanvas-title h4">Môj účet</h2>
						<button type="button" class="btn-close" data-bs-dismiss="offcanvas" data-bs-target="#panelUser"
							aria-label="Zavrieť"></button>
					</div>
					<div class="accordion" id="accUser"> 
						{SETSTYLE=accordion-user}
						{THEME_MENUAREA=102}
					</div>
				</div>
		
				<!-- REGION r - latest -->
				<div class="offcanvas-lg offcanvas-end mt-lg-3" tabindex="-1" id="panelLatest">
					<div class="offcanvas-header d-lg-none">
						<h2 class="offcanvas-title h4">Najnovšie</h2>
						<button type="button" class="btn-close" data-bs-dismiss="offcanvas" data-bs-target="#panelLatest"
							aria-label="Zavrieť"></button>
					</div>
					<div class="accordion" id="accLatest">
						{SETSTYLE=accordion-latest}
						{THEME_MENUAREA=103}
					</div>
				</div>
		
			</div>
		</div>
			</div>
		</div>
	</div>
</div>
```

* Content uses `{SETSTYLE=default}` — the main render, which feeds the page
  header. The panels use the `accordion-*` styles.
* `col-sticky`, **not** `sticky-top`: `sticky-top` pins to `top: 0` and the
  column would slide under the sticky header. `col-sticky` offsets by
  `--nav-h` and caps the column at viewport height with its own scroll — see
  [Theme layouts](README.md).

## The right column

This layout shares the right-hand pair with `3columns`: `#panelUser` and
`#panelLatest` as `offcanvas-lg`, so they are a real column from `lg` up and
drawers below it. `#panelLeft` has no column here and stays a drawer.

Menu areas 101–103, addressed with `{THEME_MENUAREA}`. See
[Shared menu areas](../guides/shared-menu-areas.md).

{% hint style="warning" %}
The content column is `col-md-9` while the side column is `col-lg-3`. Between
768px and 992px that leaves the content at 75% and the column stacked beneath
it at full width. Settle both on the same breakpoint.

The `col24-lg-19` / `col24-lg-5` pair in `style.css` exists for exactly this —
it would give the column the same width as the one in `3columns`.
{% endhint %}
