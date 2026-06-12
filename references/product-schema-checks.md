# Product / ProductGroup Schema Checks

## 2A. Product / ProductGroup

| # | Check | How to verify | Severity |
|---|---|---|---|
| 1 | Uses `ProductGroup` for multi-variant products | Look for variant count conditional logic | Critical |
| 2 | `name` field is populated (not empty) | Check Liquid output expression | Critical |
| 3 | `brand` uses `@type: "Brand"` (not `"Thing"`) | Grep for `"Thing"` near brand. Google recommends `"Brand"` explicitly. | Major |
| 4 | `image` field uses product or variant image | Check for `featured_image` or `variant.image` | Major |
| 5 | Each variant in `hasVariant` has `sku`, `price`, `availability` | Read the variant loop | Critical |
| 6 | `price` is numeric, not string-wrapped | Check for `| money` vs raw price | Major |
| 7 | `availability` maps to `schema.org/InStock` etc. | Check conditional on `variant.available` | Major |
| 8 | `url` includes variant parameter (`?variant=ID`) | Check for `variant.id` in URL | Minor |
| 9 | Currency uses `cart.currency.iso_code` (not hardcoded) | Grep for currency source | Major |

## 2A-ext. Additional Product Checks (from cross-project analysis)

| # | Check | How to verify | Severity |
|---|---|---|---|
| 9a | `image` field is present and not empty | Check for product image in schema output | Critical |
| 9b | GTIN uses correct property based on barcode length | 12 chars → `gtin12`, 13 → `gtin13`, 14 → `gtin14`, or use generic `gtin` | Minor |
| 9c | Price value is numeric (not string-wrapped by `money` filter) | `"price": 9.95` not `"price": "$9.95"` — use raw price divided by 100 or `| times: 1` | Major |

## Scoring

- Weight: /30
- Critical issue: -10
- Major issue: -5
- Minor issue: -2
