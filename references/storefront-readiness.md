# Store Readiness Checks

These go beyond schema markup — they verify store-level configuration that affects structured data quality and search visibility.

## 3A. Verifiable from code

| # | Check | How to verify | Severity |
|---|---|---|---|
| 30 | Store policies template exists (`policy.liquid` or `shop.policies` usage) | Grep for `shop.policies` or check `templates/policy*` | Critical |
| 31 | GTIN/UPC emitted in Product/variant schema | Grep for `variant.barcode` or `gtin` in JSON-LD blocks | Major |
| 32 | Standard Product Taxonomy categories in schema output | Check if `product.category` or taxonomy metafields appear in schema | Major |
| 33 | `llms.txt` file exists in theme assets | Check for `llms.txt` in assets/ directory | Nice-to-have |

## 3B. Requires admin/store access (flag as "VERIFY MANUALLY")

These cannot be confirmed from theme code alone. Flag them in the report as manual verification items.

| # | Check | How to verify | Severity |
|---|---|---|---|
| 34 | Guest checkout is enabled | Admin > Settings > Customer accounts — must be "optional" or "disabled" | Critical |
| 35 | Product descriptions are 200+ words with specs | Sample 3-5 products in admin or via Storefront MCP `search_shop_catalog` | Major |
| 36 | Product titles follow Brand + Type + Differentiator (60-80 chars) | Sample 3-5 product titles in admin or via Storefront MCP | Major |
| 37 | Alt text on product images is descriptive (not generic or empty) | Sample images in admin or via product JSON | Minor |
| 38 | Products have GTIN/barcode data populated in admin | Check via admin or `products.json` endpoint | Major |
