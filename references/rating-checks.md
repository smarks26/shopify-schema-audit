# AggregateRating Schema Checks

## 2B. AggregateRating

| # | Check | How to verify | Severity |
|---|---|---|---|
| 10 | Rating source exists (Okendo, Judge.me, Yotpo, etc.) | Grep for review app metafields | Critical |
| 11 | `ratingValue` and `reviewCount` are gated (not emitted when zero/blank) | Check conditional before schema block | Critical |
| 12 | Values are numeric (use `| plus: 0` or `| times: 1` to coerce) | Check for type coercion | Major |
| 13 | Review metafield uses `{% capture %}` before `contains` check | Metafield drops require capture to coerce to string | Major |

## Scoring

- Weight: /15
- Critical issue: -10
- Major issue: -5
