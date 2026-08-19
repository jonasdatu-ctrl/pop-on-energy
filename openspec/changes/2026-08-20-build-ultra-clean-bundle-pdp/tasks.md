## 1. Port template content

Source of truth: `../popon-shopify-store/templates/product.ultra-clean-bundle.json` (Veneers repo, sibling directory).
Target: `templates/product.ultra-clean-bundle.json` (this repo — file does not exist yet, create it).

- [x] 1.1 Read the full veneers source file.
- [x] 1.2 Build the target file's `sections` map and `order` array, porting each of the following section instances with their full block trees, `settings`, `name`, and `disabled` flags copied as-is from veneers, **except** where task 2 below says to blank a setting:

  | # | Section ID | Type | Notes |
  |---|---|---|---|
  | 1 | `customcode_featured_product_Dci89U` | `customcode-featured-product` | active hero/buy-box: title, callout[disabled], variant_selector, selling_plan, buy_button, 5 accordions (2 disabled), freebies grid |
  | — | ~~`178517016081f46b69`~~ | ~~`_blocks`~~ | **SKIP — Amazon Reviews app block, do not port** |
  | 2 | `main` | `product-information` | native fallback, keep `disabled: true` |
  | 3 | `customcode_content_generator_J6dJht` | `customcode-content-generator` | "MORE REVIEWS FROM POP ON" heading |
  | 4 | `customode_loox_reviews_nyGgWe` | `customode-loox-reviews` | Loox reviews carousel |

- [x] 1.3 Build `order` as the above sequence (1–4, skipping the Amazon Reviews row) — 4 entries total.

## 2. Strip asset references

Match by **value prefix**, not by key name — this template's `freebies` block uses non-standard keys (`image_one`..`image_four`) instead of a plain `image` key, unlike Pro Pod. Blank every setting whose value starts with `shopify://shop_images/` or `shopify://files/videos/` to `""`, regardless of what the key is called or whether the containing block is disabled:

- [x] 2.1 `customcode_featured_product_Dci89U` → block `accordion_JjE8aG` (disabled) → `settings.video` (`shopify://files/videos/tolstoy_218bf3cb-cef2-4147-802b-8ae5e9f712ee.webm`) → blank.
- [x] 2.2 `customcode_featured_product_Dci89U` → block `accordion_dtehUe` → `settings.image` (`shopify://shop_images/30_day_G.svg`) → blank.
- [x] 2.3 `customcode_featured_product_Dci89U` → block `accordion_Wxj6yq` (disabled) → `settings.image` (`shopify://shop_images/images_for_digital_impressions_in_nyc_1_1.png`) → blank.
- [x] 2.4 `customcode_featured_product_Dci89U` → block `freebies_HLBndF` → `settings.image_one`, `settings.image_two`, `settings.image_three`, `settings.image_four` (4 values) → blank all 4.
- [x] 2.5 Confirm no other `shopify://shop_images/` or `shopify://files/videos/` values remain anywhere in the file (7 total blanked: 1 video + 6 images). Done via a scripted value-prefix walk (not key-name matching, since `freebies` uses `image_one`..`image_four`) — script asserted zero remaining matches before writing the file.
- [x] 2.6 Leave every other setting on those same blocks (`video_link`, `display_html_content`, `accordion_title`, `button_text`, etc.) untouched. Verified: `accordion_JjE8aG.video_link` still `""` (its original value), not disturbed by the video blank.

## 3. Verify

- [x] 3.1 Confirm the file is valid JSON, `order` has exactly 4 entries, and every entry in `order` has a matching key in `sections` (no orphans either direction). Verified programmatically.
- [x] 3.2 Grep the finished file for `shopify://apps/` and confirm zero matches. Confirmed: zero matches.
- [ ] 3.3 Preview the Ultra Clean Bundle product page in the Shopify theme editor (once a product is assigned to this template) and confirm section order and every `disabled`/enabled state matches veneers' PDP (media slots will correctly show placeholder/empty). **Not done — no product exists yet to attach this template to, and this environment has no live Shopify admin access. Needs manual verification once a product is assigned.**
- [ ] 3.4 Confirm the `buy_button`'s payment-logo row renders (this template sets `display_payment: true`, unlike Pro Pod which left it off) — spot-check that `metaobjects.image_sets.payment_gateway_logos` resolves in Energy's admin the same way it does in veneers'. **Not done — same reason as 3.3. Note: the underlying snippet code for this branch was already confirmed byte-identical between the two repos during the Pro Pod change's investigation, so the code path itself is not a risk — only the metaobject data (`payment_gateway_logos`) needs confirming, which is store data, not theme code.**
