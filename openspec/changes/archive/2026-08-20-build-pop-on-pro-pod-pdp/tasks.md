## 1. Port template content

Source of truth: `../popon-shopify-store/templates/product.pop-on-pro-pod.json` (Veneers repo, sibling directory).
Target: `templates/product.pop-on-pro-pod.json` (this repo).

- [x] 1.1 Read the full veneers source file (it's large — read in chunks if needed).
- [x] 1.2 Rewrite the target file's `sections` map and `order` array, porting each of the following section instances with their full block trees, `settings`, `custom_css`, `name`, and `disabled` flags copied as-is from veneers, **except** where task 2 below says to blank a setting:

  | # | Section ID | Type | Notes |
  |---|---|---|---|
  | 1 | `customcode_content_generator_HYUdVU` | `customcode-content-generator` | ATC-scroll JS hook |
  | 2 | `section_zVEGgX` | `section` | "Tamar Icons with text" (stars + trust line) |
  | 3 | `customcode_featured_product_Dci89U` | `customcode-featured-product` | active hero/buy-box |
  | — | ~~`1785169794dfa76500`~~ | ~~`_blocks`~~ | **SKIP — Amazon Reviews app block, do not port** |
  | 4 | `1769789120f4c74895` | `_blocks` | empty shell, no blocks — port as-is |
  | 5 | `section_fCC9WD` | `section` | video intro |
  | 6 | `customcode_content_generator_UNapHd` | `customcode-content-generator` | sticky-ATC CTA link |
  | 7 | `section_pyjb3b` | `section` | video testimonial |
  | 8 | `customcode_content_generator_ikCBc9` | `customcode-content-generator` | sticky-ATC CTA link |
  | 9 | `customcode_tabbed_content_MhFByW` | `customcode-tabbed-content` | "How the Pro Pod Cleans" |
  | 10 | `customcode_content_generator_q4MyEE` | `customcode-content-generator` | problem/solution copy + "3 Easy Steps" lead-in |
  | 11 | `customcode_image_with_text_PJDtP9` | `customcode-image-with-text` | [disabled] alt copy block |
  | 12 | `customcode_image_with_text_NwbhTc` | `customcode-image-with-text` | "3 Easy Steps" |
  | 13 | `customcode_featured_product_Q7KjCd` | `customcode-featured-product` | [disabled] alt hero variant |
  | 14 | `customcode_grid_blocks_PRXUwt` | `customcode-grid-blocks` | [disabled] trust icon grid |
  | 15 | `customcode_tabbed_content_J3XJmm` | `customcode-tabbed-content` | [disabled] alt tech tabs |
  | 16 | `customcode_content_generator_Ae7qjQ` | `customcode-content-generator` | [disabled] step-number CSS |
  | 17 | `customcode_image_with_text_ca7jz9` | `customcode-image-with-text` | [disabled] "4 Easy Steps" |
  | 18 | `customcode_content_generator_tKghwM` | `customcode-content-generator` | sticky-ATC CTA link |
  | 19 | `customcode_accordion_alt_TqdbJK` | `customcode-accordion-alt` | FAQ accordion |
  | 20 | `customcode_content_generator_PiDKCC` | `customcode-content-generator` | sticky-ATC CTA link |
  | 21 | `customcode_content_generator_iLCiX7` | `customcode-content-generator` | [disabled] guarantee blurb |
  | 22 | `customcode_grid_blocks_zkmHrT` | `customcode-grid-blocks` | [disabled] guarantee icon grid |
  | 23 | `main` | `product-information` | native fallback, keep `disabled: true` |
  | 24 | `customcode_sticky_atc_eG3gEC` | `customcode-sticky-atc` | sticky add-to-cart bar |

- [x] 1.3 Build `order` as the above sequence (1–24, skipping the Amazon Reviews row) — 24 entries total.

## 2. Strip asset references

- [x] 2.1 Blank every `image` setting that points at `shopify://shop_images/...` to `""`. This includes:
  - `section_fCC9WD` block `image_9Vrgfw`
  - `section_pyjb3b` block `image_RKpMJN`
  - each `content_*` block's `image` setting inside `customcode_tabbed_content_MhFByW` and `customcode_tabbed_content_J3XJmm`
  - each `column_*` block's `image` setting inside `customcode_grid_blocks_PRXUwt` and `customcode_grid_blocks_zkmHrT`
  - the section-level `image` setting on `customcode_image_with_text_NwbhTc` and `customcode_image_with_text_ca7jz9`
  - Done via a scripted walk of every `settings` object rather than manual edits (20 image settings blanked, matching the expected count exactly: 1+1+4+4+4+4+1+1).
- [x] 2.2 Blank every `video` setting that points at `shopify://files/videos/...` to `""`. This includes:
  - `section_fCC9WD` block `video_KyjDPg`
  - `section_pyjb3b` block `video_WCJ8QW`
  - Leave `source`, `video_url`, `video_autoplay`, `video_loop`, `aspect_ratio`, and every other setting on those same blocks untouched.
  - Done via the same scripted walk (2 video settings blanked, as expected). Verified `source: "uploaded"` etc. left untouched.

## 3. Verify

- [x] 3.1 Confirm the rewritten file is valid JSON, `order` has exactly 24 entries, and every entry in `order` has a matching key in `sections` (no orphans either direction). Verified programmatically (JSON.parse succeeds; order/sections key sets match 1:1).
- [x] 3.2 Grep the finished file for `shopify://apps/` and confirm zero matches. Confirmed: zero matches.
- [ ] 3.3 Preview the Pro Pod product page in the Shopify theme editor. Confirm section order, and every `disabled`/enabled state, matches veneers' PDP (media slots will correctly show placeholder/empty). **Not done — requires live Shopify admin/theme-editor access, which this environment doesn't have. Needs manual verification.**
- [ ] 3.4 Spot-check a couple of the sticky-ATC CTA link sections (`customcode-content-generator` blocks containing the `#product-info-container` anchor) still work — clicking should scroll to / trigger the buy box per the existing ATC-scroll JS hook already in the file. **Not done — same reason as 3.3, needs manual verification in a live preview.**
