# Organization & Site Schema Checks

## 2D. Organization

| # | Check | How to verify | Severity |
|---|---|---|---|
| 19 | `logo` URL is dynamic (uses `shop.brand.logo`), not hardcoded path | Grep for hardcoded `/logo.png` or similar | Major |
| 20 | `logo` is conditionally omitted when brand assets not configured | Check for `shop.brand.logo != blank` guard | Minor |
| 21 | `sameAs` array has valid social URLs (not empty strings) | Check for blank guards. Prefer `shop.brand.metafields.social_links.<platform>.value` over section settings. | Major |

## 2E. BreadcrumbList

| # | Check | How to verify | Severity |
|---|---|---|---|
| 22 | Exists on product, collection, article, and page templates | Check template conditionals | Major |
| 23 | `position` values are sequential (1, 2, 3...) | Check loop counter | Minor |
| 23a | Hierarchy reflects site structure on PDP (Home → Collection → Product, not just Home → Product) | Verify the schema renders a collection title between Home and the product title. See caveat below about `product.collections.first` — themes that want deterministic hierarchy should not blindly use `.first` | Major |
| 23b | Fallback logic handles products without a collection | Look for `{% if product.collections.size > 0 %}` guard or equivalent — otherwise the schema array breaks on uncategorized products | Major |
| 23c | `item` URLs are absolute (include `shop.url`), not relative paths | Grep `"item": "/"` vs `"item": "{{ shop.url }}/"` — Google and AI crawlers require fully-qualified URLs; relative paths are silently dropped | Critical |

### Why hierarchy matters

AI agents use BreadcrumbList to understand *where* a product fits within your store. A PDP that only emits `Home → Product Title` gives the agent no taxonomy context, which degrades comparison queries like "what moisturizers do they sell?" A theme that includes a collection (`Home → Moisturizers → Product`) dramatically improves discoverability — it's the cheapest taxonomy signal a theme can provide, because the data already exists in `product.collections`.

### Caveat: `product.collections.first` is non-deterministic

When a product belongs to multiple collections, `product.collections.first` can return any of them — the array order is implementation-defined and may shift between requests. For themes that want a stable, meaningful breadcrumb (e.g., always use the most specific category, never the "All products" catch-all), prefer one of:

- An explicit metafield like `product.metafields.custom.primary_collection` pointing to the canonical collection
- A filter that skips known catch-alls (`{% assign primary = product.collections | where: 'handle', 'all' | reject: ... %}`)
- Reading the current collection from `collection` when the URL includes `/collections/<handle>/products/<handle>` (the "contextual collection" pattern)

If the theme uses `product.collections.first` without any of these strategies, flag it as a Minor issue even when check 23a passes structurally.

## Scoring

- Organization & Site weight: /10
- Breadcrumbs & Navigation weight: /10
- Critical issue: -10, Major: -5, Minor: -2
