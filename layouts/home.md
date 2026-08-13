# Home layout

**Status: 10% — just started.**

## Concept

The front page uses its own `home` layout, mapped via the special
`FRONTPAGE` custompage keyword:

```xml
<layout name='home' title='Page with full layout'>
    <custompages>FRONTPAGE</custompages>
</layout>
```

The layout is built on `css/tabler-marketing.css` (hero components:
`.hero-title`, `.hero-img`, `.img-overlap-margin`, browser mockup) rather
than the dashboard chrome. Menu areas above the hero are rendered with
`{SETSTYLE=nocaption}` so `tablestyle()` does not wrap them in cards.

## Planned

* Replace the demo hero copy with site prefs / theme shortcodes.
* Decide which sections come from menu areas vs. hardcoded layout markup.
* Real-estate content sections (listings teaser, contact CTA).
