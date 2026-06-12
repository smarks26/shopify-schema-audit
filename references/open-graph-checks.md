# Open Graph Meta Tag Checks

## Scope note

Open Graph tags are **not** JSON-LD — they're `<meta>` tags in the `<head>`. They're included in this audit because AI agents, social platforms, and search engines use them as a **parallel structured signal** when the JSON-LD is incomplete or absent. A product page without OG tags loses previews in link shares and is less visible to agents that crawl lightweight metadata first (ChatGPT browsing, Slack/Discord link previews, Facebook/LinkedIn shares).

## What Shopify provides automatically

Two facts matter before running the checks, because they change what the theme is responsible for:

1. **`{{ content_for_header }}` does NOT inject Open Graph tags.** It injects scripts required by Shopify (analytics, theme editor, app pixels). OG tags live in the theme's own layout or a meta-tags snippet.
2. **`og:image` has a built-in fallback.** If the theme doesn't emit an `og:image` tag, Shopify automatically generates `og:image`, `og:image:secure_url`, `og:image:width`, and `og:image:height` from the [`page_image`](https://shopify.dev/docs/api/liquid/objects/page_image) object (resource's featured image for product/collection/article pages; the store's social sharing image elsewhere). This means missing `og:image` in the theme is usually not a bug — Shopify filled it in. If the theme does emit one, it's responsible for the entire image set.

Everything else — `og:title`, `og:description`, `og:url`, `og:type` — must be emitted by the theme. Shopify does not fall back on these.

## 2G. Open Graph Tags

| # | Check | How to verify | Severity |
|---|---|---|---|
| 43 | `og:title` is present and uses the page-specific title (not the shop name) on product and article pages | View page source or grep `.liquid` for `og:title` — ensure content resolves to `product.title` / `article.title` / `page.title`, not `shop.name` on every page | Major |
| 44 | `og:description` is present and uses product description or page excerpt (not a single default shop tagline repeated on every page) | Grep for `og:description` — the Liquid expression must differentiate by template (PDP → product, blog → article, etc.) | Major |
| 45 | If the theme emits `og:image`, it uses an absolute URL with explicit width and also emits the companion `og:image:secure_url`, `og:image:width`, `og:image:height` tags. If the theme omits `og:image` entirely, Shopify's `page_image` fallback covers it — verify products/articles have `featured_image` populated. | Grep `.liquid` for `og:image` — if present, check `image_url: width: ...` (not `.src`, not protocol-relative `//cdn.shopify.com/...`); if absent, spot-check rendered HTML to confirm Shopify's fallback fires | Minor |
| 46 | `og:url` is set to the canonical URL for the current page | Check `og:url` uses `canonical_url` or `shop.url + request.path` — not a hardcoded domain | Minor |
| 47 | `og:type` is `product` on PDP, `article` on blog posts, `website` elsewhere | Grep for conditional on `request.page_type` or template-specific overrides. Note: `request.page_type` values like `blog`, `index`, `page` should all map to `og:type = "website"`, not repeated as-is | Minor |

### Common failures

- Apps (live chat, review widgets, analytics) injecting their own `<meta>` tags that duplicate or override OG tags — causing different crawlers to read different data.
- Theme emits `og:image` but forgets the companion `:width` / `:height` / `:secure_url` tags, causing some crawlers to reject the preview.
- Using `product.featured_image.src` directly instead of `product.featured_image | image_url: width: 1200` — resulting in protocol-relative or sized-down URLs rejected by some crawlers.
- Custom pages that override `<head>` without including `{{ content_for_header }}` — this doesn't break OG tags directly (content_for_header doesn't emit OG), but it often signals a larger meta-tag hygiene problem worth investigating.
- Mapping `request.page_type` values 1:1 to `og:type` — e.g., emitting `og:type = "blog"` or `og:type = "index"`, both invalid per the Open Graph protocol.

## Scoring

- Open Graph weight: /10
- Critical issue: -10, Major: -5, Minor: -2
