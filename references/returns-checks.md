# Return Policy Schema Checks

## 2F. MerchantReturnPolicy

AI agents use this schema to answer customer questions about returns ("do they offer free returns?", "how long do I have to return?") without routing the user to a policy page. Shopify exposes return-related data via `shop.policies` but does **not** emit `MerchantReturnPolicy` JSON-LD automatically — it must be hand-written in the theme.

This schema is also consumed by Google Shopping's free listings to surface return-policy badges in product results.

| # | Check | How to verify | Severity |
|---|---|---|---|
| 39 | `MerchantReturnPolicy` JSON-LD block exists (embedded in `Offer` or as top-level schema) | Grep `.liquid` files for `"@type": "MerchantReturnPolicy"` | Major |
| 40 | `merchantReturnDays` is present and a valid type (Integer is most common; Date or DateTime also accepted per schema.org for fixed return dates). Should not render as empty string or zero. | Check hardcoded or metafield-sourced value — ensure it does not render as `"merchantReturnDays": ""` or `0`. If sourced from a text metafield like `"30 days"`, normalize to `30` with `\| plus: 0` or `\| split: ' ' \| first \| plus: 0` | Major |
| 41 | `returnPolicyCategory` uses a valid schema.org URL (e.g., `https://schema.org/MerchantReturnFiniteReturnWindow`, `https://schema.org/MerchantReturnUnlimitedWindow`, `https://schema.org/MerchantReturnNotPermitted`) | Grep for the full schema.org URL. The schema accepts enum instances of `MerchantReturnEnumeration` — using the short suffix alone (e.g., `"MerchantReturnFiniteReturnWindow"`) instead of the full URL is a common mistake that Google's Rich Results Test flags | Major |
| 42 | `applicableCountry` matches the market this schema renders in. Can be either a full `Country` object or a two-letter ISO 3166-1 alpha-2 text code. | For multi-market stores, verify the value varies by locale (`localization.country.iso_code`) rather than being hardcoded per market. Single-market stores may hardcode a two-letter string (e.g., `"CA"`) | Minor |

### Why this matters

Adding `MerchantReturnPolicy` is low-effort — typically 4-6 lines of Liquid inside the existing `Offer` block — and directly affects how AI agents present your store in comparison results. Merchants with missing or malformed return-policy data get paraphrased or omitted from AI responses even when the policy is generous.

Common pitfall: storing the return window in a shop-level metafield as a text string (`"30 days"`) instead of an integer (`30`). Schema.org accepts Integer, Date, or DateTime for `merchantReturnDays`, but a string like `"30 days"` is none of those — it will render as a string in JSON-LD and crawlers will ignore it.

## Scoring

- Returns & Policy weight: /10
- Critical issue: -10, Major: -5, Minor: -2
