## Why

`templates/product.pop-on-whitening-amazon.json` already exists in Energy as a substantial, independently-maintained PDP (19.6KB, not a stub) — the last of the four templates worked through in this line of changes, and the first one that turns out to be genuinely different in kind from the other three.

Comparing section-by-section against veneers' version (33.9KB) shows two things at once:

1. **Two structural gaps**, cleanly missing from Energy: a "Trending On TikTok" content section (`customcode_content_generator_tn8qXi`, a Firework/`fw-embed-feed` embed), and a second `_blocks` section whose only content is another Amazon Reviews app-block widget.
2. **Every section that exists in both files has already been independently rewritten in Energy** — not stale, not a copy that fell behind, but deliberate divergent content. The "Product Details" accordion's bullet copy has been substantively reworded (a whole bullet point replaced with a different product claim), the warranty accordion was retitled and shortened, the reviews-section heading was rewritten entirely, and the Shipping Information accordion is enabled in Energy while veneers keeps it disabled — the opposite direction of the accordion-state difference found in the default-template change.

This means the "build out as per veneers" approach used for the previous three templates does not apply cleanly here. Porting veneers' content into the overlapping sections would overwrite real copywriting already done specifically for this Energy product — a regression, not a build-out. **Explicitly confirmed during exploration: scope this change to only the two clean gaps, and leave every already-diverged section untouched.**

The Firework/TikTok embed is not veneers-exclusive infrastructure — Energy already uses the same `fw-embed-feed` web component and the same `channel="pop_on"` value elsewhere (the homepage template and `customcode-text-marquee.liquid`'s own default), so this is a shared umbrella integration, not something new being introduced.

## What Changes

- Add `sections.customcode_content_generator_tn8qXi` to `templates/product.pop-on-whitening-amazon.json`, copied verbatim from veneers — the "Trending On TikTok" Firework embed (`channel="pop_on"`, same channel Energy already uses elsewhere).
- Do not port `1785170463ff170f29` (the second `_blocks` section, containing an Amazon Reviews app-block widget) — dropped, consistent with the precedent from all three prior changes in this line of work.
- Insert the new section into `order` at the same relative position veneers uses (immediately after the hero, `customcode_featured_product_Dci89U`).
- **No other section in this file changes.** `customcode_featured_product_Dci89U` (hero, including its already-rewritten accordions and buy button), `1768322716829cb631` (image gallery — has a minor cosmetic styling difference from veneers, explicitly left alone), `main`, `customcode_content_generator_Qk9iPc`, `customcode_content_generator_GYfgzK` (reviews heading, already rewritten), and `customode_loox_reviews_NUXYAQ` are all left byte-for-byte as they exist in Energy today.
- No `.liquid` section, snippet, or block file changes — `ai_gen_block_fe68796.liquid` and the Firework integration are already present and in use elsewhere in Energy.
- No image/video assets to blank in the added section (the Firework embed has no `shopify://` asset references).

## Capabilities

### New Capabilities
None.

### Modified Capabilities
None — this is theme content/configuration (one JSON template), not a product-facing requirement or spec change.

## Impact

- `templates/product.pop-on-whitening-amazon.json`: modified in place. Sections grow from 6 to 7 (one addition); `order` grows from 6 to 7 entries.
- No other files change.
- Energy's already-diverged marketing copy (Product Details bullets, warranty accordion title/text, Shipping accordion's enabled state, reviews heading wording) is explicitly preserved, not overwritten — the core decision of this change.
- This template is product-specific (like Pro Pod and Ultra Clean Bundle), not the site-wide default, so the blast-radius concerns from the default-template change don't apply here — only the one whitening-strips product using this template is affected.
