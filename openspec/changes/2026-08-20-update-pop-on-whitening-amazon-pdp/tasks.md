## 1. Re-verify scope before editing

- [x] 1.1 Re-ran the check — confirmed unchanged since the proposal was written: `order` still had 6 entries, `customcode_content_generator_tn8qXi` and `1785170463ff170f29` both still absent.

## 2. Add the Trending On TikTok section

Source: `../popon-shopify-store/templates/product.pop-on-whitening-amazon.json` (veneers).

- [x] 2.1 Added `sections.customcode_content_generator_tn8qXi`, copied verbatim from veneers (the `liquid_rfiXY6` block containing the `fw-embed-feed` Firework embed, `channel="pop_on"`, plus its `custom_css` rule).
- [x] 2.2 Confirmed `1785170463ff170f29` (Amazon Reviews app-block `_blocks` section) not ported — script asserted its absence.

## 3. Update `order`

- [x] 3.1 Inserted `"customcode_content_generator_tn8qXi"` into `order` immediately after `"customcode_featured_product_Dci89U"`. Resulting `order` (7 entries) matches exactly as specified.

## 4. Leave everything else untouched

- [x] 4.1 Confirmed all 6 pre-existing sections semantically unchanged, both self-consistently (within the edit script) and against `git show HEAD:...` (the committed pre-edit version) — all six returned `true` on a parsed-value comparison.

## 5. Verify

- [x] 5.1 Confirmed valid JSON, `order` has exactly 7 entries, and `order`/`sections` keys match 1:1.
- [x] 5.2 Grepped the finished file for `shopify://apps/` — zero matches.
- [x] 5.3 Confirmed energy's rewritten copy is intact: "CLINICALLY PROVEN RESULTS WITHOUT SENSITIVITY" bullet present, warranty accordion still titled "Description", `accordion_wjdWUJ` still enabled, reviews heading still "What are customers saying?" — all asserted programmatically before the file was written.
- [ ] 5.4 **Manual, in Shopify admin — not doable from this environment:** preview the whitening-strips product page and confirm the new "Trending On TikTok" section renders the Firework feed correctly, positioned right after the hero.
