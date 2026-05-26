# hey-august.github.io
GitHub Pages site with Hugo

## Theme compatibility

This site uses the [hugo-theme-til](https://github.com/michenriksen/hugo-theme-til) theme, which targets Hugo < v0.156.0. Local template overrides in `layouts/` patch deprecated/removed APIs:

| Theme file | Local override | Fix |
|-----------|---------------|-----|
| `partials/head.html` | `layouts/partials/head.html` | `site.Author` → `site.Params.author.name` |
| `404.html` | `layouts/404.html` | `.Site.Author.email` → `site.Params.author.email` |
| `_default/baseof.html` | `layouts/_default/baseof.html` | `Language.LanguageCode` → `Language.Locale`, `Language.LanguageDirection` → `Language.Direction` |
| `_default/_markup/render-heading.html` | `layouts/_default/_markup/render-heading.html` | Pass explicit context to `svg/Link.html` |
| `partials/svg/Link.html` | `layouts/partials/svg/Link.html` | Handle missing `.class` on heading context |

Remove these overrides when the theme updates to support Hugo ≥ v0.158.0.
