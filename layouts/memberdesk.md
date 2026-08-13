# Memberdesk layout

Member-area pages; intended for the custom plugins currently in
preparation. No `custompages` yet — pages are assigned manually until the
plugins land.

## Structure

```html
<aside class="navbar navbar-vertical navbar-expand-sm position-absolute" data-bs-theme="dark">
	<div class="container-fluid">
		{SETSTYLE=card}
		{MENUAREA=100}
	</div>
</aside>
<div class="page-wrapper">
	<div class="page-body">
		<div class="container-xl">
			{SETSTYLE=card}
			{---}
		</div>
	</div>
</div>
```

## Rules specific to this layout

* Vertical dark sidebar on the **left** replaces the horizontal header —
  the header partial is suppressed (`sc_header` returns `''`).
* The footer uses the reduced variant `footers/footer_small.html`
  (`sc_footer` switch) — see
  [Header & footer partials](header-footer.md).
* Menu area **100** in the sidebar.
