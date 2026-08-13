# Sidebar layout

Magic page header + content with a menu column on the **right**
(`custompages: news`). Pattern: Tabler `job-listing.html`.

## Structure

```html
<div class="page-wrapper">
	<div class="page-header d-print-none" aria-label="Page header">
		<div class="container-xl">
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
		<div class="container-xl">
			<div class="row g-4">
				<div class="col-md-9">
					{SETSTYLE=default}
					{---}
				</div>
				<aside class="col-md-3">
					<div class="sticky-top">
						{SETSTYLE=card}
						{MENUAREA=100}
					</div>
				</aside>
			</div>
		</div>
	</div>
</div>
```

* Content uses `{SETSTYLE=default}` (main render → feeds the page header);
  the sidebar switches to `{SETSTYLE=card}` for its menus.
* The `sticky-top` wrapper keeps the menu column visible while scrolling.
* Menu area **100** — assign menus in the Menu Manager.
