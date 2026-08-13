# Page layout

The layout for standalone pages (`custompages: page faq`): boxed page with the Tabler
page header filled by the magic shortcodes.

The .page wrapper and both navbars come from theme.html + headers/header_default.html; this file starts at .page-wrapper.
The page header is filled by the magic shortcodes {---CAPTION---} and {---BREADCRUMB---} - they are populated from the MAIN render, which is  what {SETSTYLE=default} designates. 

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
			{SETSTYLE=default}
			{---}
		</div>
	</div>
</div>
```

{% hint style="danger" %}
Never switch this layout to `{SETSTYLE=card}` — the magic shortcodes are
fed by the `default`-styled main render, and with `card` the page header
stays empty. See the
[magic shortcodes section](README.md).
{% endhint %}

The page-header markup may later move into `tablestyle()` if a suitable
template mechanism is found.
