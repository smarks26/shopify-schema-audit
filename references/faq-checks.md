# FAQPage Schema Checks

## 2C. FAQPage

| # | Check | How to verify | Severity |
|---|---|---|---|
| 14 | `mainEntity` is an array of `Question` objects | Check JSON structure | Critical |
| 15 | Each `Question` has `acceptedAnswer` with `@type: "Answer"` | Check nested structure | Critical |
| 16 | Answer text uses `strip_html` for clean output | Grep for `strip_html` | Major |
| 17 | Richtext metafields use safe text extraction | If schema code uses `strip_html` but display code uses `metafield_tag` for the same field, flag as risk. Metafield type cannot be confirmed from Liquid alone. **Preferred:** use `| metafield_text` filter (returns plain text for `rich_text_field` per Shopify docs) instead of `metafield_tag` + `capture` + `strip_html`. | Critical |
| 18 | No trailing comma in JSON array (proper `forloop.last` check) | Check comma logic in loop | Major |

## Scoring

- Weight: /15
- Critical issue: -10
- Major issue: -5
