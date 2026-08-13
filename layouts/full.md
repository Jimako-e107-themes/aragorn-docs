# Full layout

Full page with the horizontal header and boxed content width. Registered
without `custompages` — assigned manually where needed.

## Structure

```html
<div class="page-wrapper">
	<div class="page-body">
		<div class="container-xl">
			{SETSTYLE=card}
			{---}
		</div>
	</div>
	<footer class="footer footer-transparent d-print-none"> ... {SITEDISCLAIMER} ... </footer>
</div>
```

* Starts at `.page-wrapper` — the `.page` wrapper and both navbars come
  from [theme.html](../skeleton/theme-html.md) + the
  [header partial](header-footer.md).
* Content renders as Tabler cards via `{SETSTYLE=card}`.
* Small transparent footer with `{SITEDISCLAIMER}`; the big site footer is
  the shared [footer partial](header-footer.md).
