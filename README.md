# shopify-schema-audit

Audit **Shopify Liquid themes** (Dawn, Horizon, custom OS 2.0, etc.) for structured data (JSON-LD) quality — Product, AggregateRating, Organization, FAQ, BreadcrumbList, MerchantReturnPolicy, Open Graph meta tags, and conflict detection.

An [agent skill](https://github.com/vercel-labs/skills) for Claude Code, Cursor, Codex, and other AI coding tools.

> **Scope:** Liquid themes only. Not for headless projects (Hydrogen, Next.js, Nuxt) or Shopify apps.

## What it does

Runs a structured 4-phase audit on your Shopify theme codebase:

1. **Discovery** - Finds all JSON-LD structured data implementations
2. **Schema & Meta Validation** - Checks 44 items across Product, AggregateRating, FAQPage, Organization, BreadcrumbList, MerchantReturnPolicy, global JSON-LD patterns, and Open Graph meta tags
3. **Store Readiness** - Validates store-level config that affects structured data quality (9 checks)
4. **Report Generation** - Outputs a `SCHEMA_AUDIT_REPORT.md` with score (0-100), issues by severity, and fix suggestions

## Install

```bash
npx skills add smarks26/shopify-schema-audit
```

## Usage

After installing, the skill activates automatically when you work on a Shopify Liquid theme project. You can also invoke it directly:

- **Full audit** (new project): "Run a full schema audit on this theme"
- **Schema check** (during dev): "Check the JSON-LD schema I just added"
- **Pre-deploy gate** (before PR): "Validate structured data before I open a PR"

## Structure

```
shopify-schema-audit/
├── SKILL.md                              # Main workflow (lean overview)
├── references/
│   ├── product-schema-checks.md          # Product/ProductGroup checks (1-9)
│   ├── rating-checks.md                  # AggregateRating checks (10-13)
│   ├── faq-checks.md                     # FAQPage checks (14-18)
│   ├── organization-checks.md            # Organization + BreadcrumbList checks (19-23c)
│   ├── returns-checks.md                 # MerchantReturnPolicy checks (39-42)
│   ├── global-checks.md                  # Global checks for all schema blocks (24-29, 29a-29c)
│   ├── open-graph-checks.md              # Open Graph meta tag checks (43-47)
│   ├── storefront-readiness.md           # Store-level config checks (30-38)
│   └── common-mistakes.md                # Edge cases from real projects
├── templates/
│   └── report-template.md                # Output report template
├── README.md
├── LICENSE
└── .gitignore
```

The skill uses **progressive disclosure** - SKILL.md is the overview, and reference files are loaded only when needed for the specific checks being run.

## What it checks

| Category | Checks | Weight |
|----------|--------|--------|
| Product / ProductGroup schema | ProductGroup for variants, brand type, GTIN, pricing, availability | /30 |
| AggregateRating | Review app integration, numeric gating, metafield coercion | /15 |
| FAQPage | Structure, richtext handling, JSON comma logic | /15 |
| Organization & Site | Logo source, sameAs social links, WebSite + SearchAction | /10 |
| BreadcrumbList | Template coverage, hierarchy, fallback logic, absolute URLs | /10 |
| MerchantReturnPolicy | Schema presence, return days, valid category URLs, country targeting | /10 |
| Open Graph meta tags | og:title, og:description, og:image, og:url, og:type per template | /10 |
| Store Config | Policies, GTIN exposure, taxonomy, guest checkout | /20 |

## Common Mistakes it catches

Based on real bugs encountered across multiple production Shopify themes:

- Okendo/review app metafield `contains` returning false (needs `{% capture %}` first)
- Richtext metafields outputting raw JSON in schema (use `| metafield_text` filter)
- Hardcoded logo URLs returning 404 (use `shop.brand.logo`)
- String comparison on numeric review values (normalize with `| plus: 0`)
- Organization `sameAs` with empty strings (use `shop.brand.metafields.social_links`)
- Wrong metafield keys (e.g., `custom.lrv` vs `custom.light_reflectance_value`)
- Duplicate schema from SEO apps (SEOManager, SchemaApp) conflicting with theme schema
- Relying solely on Shopify's native `{{ product | structured_data }}` filter (limited output)
- Price formatted as currency string (`"$9.95"`) instead of number (`9.95`)
- Microdata and JSON-LD mixed for the same schema type on one page

## Compatibility

Works with any AI coding tool that supports the [agent skills](https://github.com/vercel-labs/skills) standard:

Claude Code, Cursor, OpenAI Codex, GitHub Copilot, Gemini CLI, Windsurf, Cline, and others.

## License

MIT
