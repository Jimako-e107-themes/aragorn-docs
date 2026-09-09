# Styling

**Status: 85% — the file boundaries hold; a Sass build is still to come.**

## What it is

Aragorn's CSS is four files, loaded in this order by `theme.php :: css()`:

```php
e107::css('theme', 'css/tabler.min.css');        // 1. the framework
e107::css('theme', 'css/tabler-marketing.css');
e107::css('theme', 'css/tabler-themes.min.css');
e107::css('theme', 'css/brand-e107sk.css');      // 2. the palette
e107::css('theme', 'style.css');                 // 3. the theme's own
```

Order is the whole design. Each file can override everything before it, so the
question that decides where a rule goes is *what may it be allowed to beat?*

## Which file a rule belongs in

| File | Holds | Test |
| --- | --- | --- |
| `css/brand-<site>.css` | colour, and only colour | would a second site with a different palette need a different value? |
| `style.css` | structure, spacing, compatibility fixes | would every site need this, whatever its colours? |
| `css/tabler*.css` | nothing of ours | never edited — it is a vendor build |

{% hint style="danger" %}
`style.css` loads **after** the brand file, so a colour set there silently
overrides the palette — including its contrast checks, which are the whole
reason the palette annotates ratios. Nothing brand-related belongs in
`style.css` for exactly that reason.
{% endhint %}

The reverse is just as easy to get wrong: padding, font sizes and layout
fixes in a brand file mean the next site inherits somebody else's spacing
along with their colours. Keep the brand file to values that would change
if the logo changed.

## The chapters

- **[Working with Tabler variables](tabler-variables.md)** — why setting a
  `--tblr-*` variable on a wrapper usually does nothing, and the five places
  in Tabler where that bites. Read this first; it explains most rules that
  "do not work".
- **[Brand files](brand-files.md)** — the three-layer structure of a palette
  file, the markup hooks it depends on, and what may not appear in a template
  because no stylesheet can override it.
- **[Compatibility shims](compat-shims.md)** — the Bootstrap 3 and 4 class
  names e107 still emits, what Bootstrap 5 did with them, and which of them
  need a patch rather than a template rewrite.

## Conventions

**Comment the reason, not the rule.** A selector says what it does; the
comment should say why it exists and what would let it be deleted. Anything
longer than a couple of lines belongs in these chapters instead, with the code
carrying a one-line pointer.

**Cite the source.** Rules that work around framework behaviour name the file
and line they are working around — `tabler.css:4121` and the like. Those
references are what make it possible to check, on the next upgrade, whether
the workaround is still needed.

**Note the contrast ratio** next to any colour chosen for accessibility rather
than appearance. Without it, the next person reuses a decorative colour as a
text colour.
