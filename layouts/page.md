# Page layout

The **default** layout (`custompages: page faq`): boxed page with the Tabler
page header filled by the magic shortcodes.

## Structure

```html
<div class="page-wrapper">
	<div class="page-header d-print-none" aria-label="Page header">
		... <h2 class="page-title">{---CAPTION---}</h2> ... {---BREADCRUMB---} ...
	</div>
	<div class="page-body">
		<div class="container-xl">
			{SETSTYLE=default}
			{---}
		</div>
	</div>
	<footer class="footer footer-transparent d-print-none"> ... </footer>
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
