# Auth layout

Centered wrapper for the auth pages (`custompages: login fpw signup`).
Formerly named `splash`.

## Structure

```html
<div class="container container-tight py-4">
	<div class="text-center mb-4">
		<a href="{SITEURL}" class="navbar-brand navbar-brand-autodark d-inline-flex flex-column align-items-center" aria-label="{SITENAME}">
			{SITELOGO: h=40}
			<span class="mt-2">{SITENAME}</span>
		</a>
	</div>
	{---}
</div>
```

## Rules specific to this layout

* The centering comes from `{PAGE_CLASS}` returning `page-center` — the
  shared `.page` wrapper in theme.html becomes `.page.page-center`.
* The **header and footer partials are suppressed** for this layout
  (`sc_header` / `sc_footer` return `''`) — inside the `page-center` flex
  they would break the centered card, and Tabler auth pages carry no
  navbar.
* The card around the form comes from `tablestyle()` (`login_page` / `fpw`
  / `signup` modes), not from the layout — that keeps the standard and the
  members-only render paths identical. The full members-only story is on
  [Auth pages](../standalone/auth-pages.md).
