# Common Mistakes (from real Shopify theme projects)

## Okendo/review app metafield `contains` returns false

**Symptom:** `product.metafields.okendo.StarRatingSnippet contains '"averageRating"'` returns `false` even when the HTML has the string.

**Cause:** Metafield drops are objects, not strings. `contains` doesn't coerce.

**Fix:** Use `{% capture %}` first to render as string, then `contains`/`split`.

## Richtext metaobject outputs raw JSON in schema

**Symptom:** `acceptedAnswer.text` shows `{"type":"root","children":[...]}` instead of clean text.

**Cause:** `strip_html` on a richtext metaobject field doesn't work — the value is a structured object.

**Fix (best):** Use `{{ field | metafield_text }}` — Shopify's `metafield_text` filter returns plain text for `rich_text_field` types (per [Shopify docs](https://shopify.dev/docs/api/liquid/filters/metafield_text)).

**Fix (alternative):** `{% capture html %}{{ field | metafield_tag }}{% endcapture %}{{ html | strip_html }}`.

## Hardcoded logo URL returns 404

**Symptom:** Organization schema `logo.url` points to `/logo.png` but returns 404.

**Cause:** Theme uses inline SVG logo, no file at that path.

**Fix:** Use `shop.brand.logo | image_url: width: 300 | prepend: 'https:'` with a blank check.

## Organization sameAs uses section settings instead of brand metafields

**Symptom:** `sameAs` array contains empty strings or breaks when section settings change.

**Cause:** Social URLs pulled from section/theme settings instead of Shopify's native brand data.

**Fix:** Use `shop.brand.metafields.social_links.<platform>.value` (supports: facebook, pinterest, instagram, tiktok, tumblr, snapchat, vimeo). Always wrap in `{% if url != blank %}` guard before adding to the array.

## String comparison on numeric review values

**Symptom:** `aggregateRating` emits with `ratingValue: "0"` when no reviews exist.

**Cause:** Comparing metafield value (string) to number `0` — Liquid doesn't auto-coerce.

**Fix:** Normalize with `| plus: 0` before comparison: `assign rating_num = oke_rating | plus: 0`.

## Wrong metafield key assumed

**Symptom:** Metafield renders blank even though data exists in admin.

**Cause:** Key mismatch (e.g., `custom.lrv` vs `custom.light_reflectance_value`).

**Fix:** Always verify key by checking the admin metafield definition URL or grepping the reference project. Category metafields with hyphens need bracket notation: `product.metafields.shopify['intended-application']`.

## CSS specificity collision on injected schema-related content

**Symptom:** Metafield-driven content (e.g., PDP description) renders with wrong styles.

**Cause:** Content injected inside `metafield-rich_text_field` wrapper inherits its styles.

**Fix:** Use tag-qualified selectors (`p.your-class`) to match or beat the existing specificity.

## Duplicate schema from SEO apps conflicting with theme schema

**Symptom:** Google Search Console shows duplicate `Product` or `Organization` entries. Rich results flicker or disappear.

**Cause:** Third-party apps (SEOManager, SchemaApp, JSON-LD for SEO, Smart SEO) inject their own JSON-LD that overlaps with the theme's schema. Common in projects like ThirdLove where SEOManager generates 447 lines of schema alongside manual theme schema.

**Fix:** Choose one source of truth — either the app OR the theme. If keeping the app, remove the theme's JSON-LD for the same types. If keeping the theme, disable the app's schema output for those types.

## Relying solely on Shopify's native `structured_data` filter

**Symptom:** Product pages have basic schema but no AggregateRating, FAQPage, or custom fields. Schema is minimal.

**Cause:** Using `{{ product | structured_data }}` — Shopify's built-in filter that generates basic JSON-LD automatically but with no customization.

**Fix:** Replace with custom JSON-LD that includes AggregateRating, variant details, GTIN, and any metafield data relevant to the product.

## Price formatted as currency string instead of number

**Symptom:** Google Search Console error: "price field value is invalid."

**Cause:** Using `| money` or `| money_with_currency` filter inside JSON-LD, outputting `"$9.95"` instead of `9.95`.

**Fix:** Use raw price divided by 100: `{{ variant.price | divided_by: 100.0 }}` or use `| money_without_currency | strip_html` and ensure no currency symbol.

## Person/Organization schema with hardcoded URL

**Symptom:** Person schema `url` points to homepage (`https://store.com/`) instead of the actual profile page.

**Cause:** URL field hardcoded to `shop.url` instead of the current page URL.

**Fix:** Use `{{ canonical_url }}` or `{{ request.origin }}{{ request.path }}` for the current page URL.

## BreadcrumbList schema disabled by default via section setting

**Symptom:** BreadcrumbList code exists in the theme but never renders.

**Cause:** The section has an `output_schema` boolean setting defaulting to `false`. Easy to miss during audits because the code looks complete.

**Fix:** Enable in theme editor or change default to `true` in the section's schema settings block.

## Microdata and JSON-LD mixed for the same schema type

**Symptom:** Google shows unexpected rich results or ignores schema entirely.

**Cause:** Product page has both `itemscope itemtype="schema.org/Product"` attributes in HTML AND a separate JSON-LD `@type: "Product"` block. Google recommends using one format per type.

**Fix:** Pick one format (JSON-LD recommended) and remove the other. If microdata is embedded in the template HTML and hard to remove, at minimum ensure both outputs are consistent.
