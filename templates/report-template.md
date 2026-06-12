# Schema Audit Report

**Project:** [theme name]
**Date:** [date]
**Auditor:** [agent or person]
**Mode:** Full / Schema Check / Pre-deploy Gate

## Score: [X]/100

### Breakdown

| Category | Score | Issues |
|---|---|---|
| Product Schema | /30 | [count] |
| Rating & Reviews | /15 | [count] |
| FAQ Schema | /15 | [count] |
| Organization & Site | /10 | [count] |
| Breadcrumbs & Navigation | /10 | [count] |
| Returns & Policy (MerchantReturnPolicy) | /10 | [count] |
| Open Graph Meta Tags | /10 | [count] |
| Store Config | /20 | [count] |

### Critical Issues (fix before deploy)

1. [issue + file:line + fix suggestion]

### Major Issues (fix soon)

1. [issue + file:line + fix suggestion]

### Minor Issues (nice to have)

1. [issue + file:line + fix suggestion]

### Manual Verification Required

Items that cannot be confirmed from theme code alone:

- [ ] Guest checkout enabled (Admin > Settings > Customer accounts)
- [ ] Product descriptions 200+ words with specs
- [ ] Product titles follow Brand + Type + Differentiator format
- [ ] Product images have descriptive alt text
- [ ] GTIN/barcode data populated on variants

### Schema Coverage Matrix

| Schema Type | File | Template Context | Status |
|---|---|---|---|
| Product / ProductGroup | ? | product template | ? |
| AggregateRating | ? | product template | ? |
| FAQPage | ? | product, collection, FAQ page | ? |
| Organization | ? | homepage, author pages | ? |
| WebSite + SearchAction | ? | homepage | ? |
| BreadcrumbList | ? | all templates | ? |
| MerchantReturnPolicy | ? | product template (inside Offer) or global | ? |
| CollectionPage + ItemList | ? | collection template | ? |
| Article | ? | blog article template | ? |
| Blog | ? | blog index template | ? |
| Person | ? | author pages | ? |
| Offer | ? | inside Product/ProductGroup | ? |

### What's Working Well

[list of correctly implemented schemas]
