# Global Schema Checks

These apply to ALL JSON-LD blocks in the theme.

## 2F. Global Checks

| # | Check | How to verify | Severity |
|---|---|---|---|
| 24 | `@context` uses `https://schema.org` consistently (not `http://`) | Grep for `http://schema.org` | Minor |
| 25 | No hardcoded URLs (use `shop.url`, `request.origin`, Liquid objects) | Grep for hardcoded domains | Major |
| 26 | JSON-LD is rendered before `content_for_layout` (in `<head>` or top of `<body>`) | Check include placement in layout/theme.liquid | Minor |
| 27 | JSON output is valid (no unescaped quotes, proper `| json` filter usage) | Check for `| json` on dynamic string values | Critical |
| 28 | Date fields use `| date` filter (not raw `created_at` output) | Check Article/Blog date fields in schema | Major |
| 29 | Variant selection dynamically updates schema (or ProductGroup covers all) | Check for JS-driven schema updates or ProductGroup pattern | Minor |

## 2F-ext. Additional Global Checks

| # | Check | How to verify | Severity |
|---|---|---|---|
| 29d | `<script type="application/ld+json">` has no extra attributes (`async`, `defer`, `class`) | Grep for `ld+json` and check for extra attributes on the tag | Minor |
| 29e | `priceValidUntil` is not arbitrarily hardcoded (e.g., "+365 days from now") | Check if tied to actual offer/stock data, not `"today" \| date: '%s' \| plus: 31536000` | Minor |
| 29f | `@id` URLs are properly constructed (no double domain concatenation) | Check for patterns like `shop.url + routes.root_url` that may produce `https://store.com/https://...` | Major |

## 2G. Conflict Detection (cross-source schema issues)

| # | Check | How to verify | Severity |
|---|---|---|---|
| 29a | No duplicate schema from SEO apps | Grep for known app markers: `SEOManager`, `SchemaApp`, `json-ld-for-seo`, `smart-seo`. Check if theme also generates its own schema for the same type. | Critical |
| 29b | No mixing of Microdata and JSON-LD for the same schema type | If Product uses `itemscope itemtype="schema.org/Product"` AND JSON-LD `@type: "Product"`, flag as conflict. Google recommends one format per type. | Major |
| 29c | Not relying solely on Shopify's native `structured_data` filter | Grep for `{{ product \| structured_data }}` or `{{ article \| structured_data }}`. This filter is a black-box with limited output — no AggregateRating, no FAQPage, no custom fields. Flag if it's the only schema source. | Major |
