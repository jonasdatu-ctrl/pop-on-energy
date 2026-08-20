## 1. Verify the diff scope before editing

- [x] 1.1 Confirmed (already done during proposal-writing, re-verify if re-implementing later) that `customcode_featured_product_HRwfDi` is otherwise identical between energy and veneers aside from: the `upload_lift_app_block_B7z4ex` block (veneers only), and the three accordions' `disabled` state (veneers active, energy disabled — **leave energy's disabled state untouched**, do not port veneers' active state). A structural diff run during design confirmed no other real differences — `variant_selector_pVYxPC` settings differ only in JSON key order (same values), and energy's accordions lack a `custom_html` key that's present-but-empty in veneers (expected, ties to the known `customcode-accordion.liquid` snippet gap, not a new issue). Re-ran the same check immediately before editing — unchanged.

## 2. Remove the Upload Lift app block

- [x] 2.1 Checked `templates/product.json`'s `sections.customcode_featured_product_HRwfDi.blocks` — **the Upload Lift block was never present in energy's file to begin with** (confirmed during the original exploration and re-confirmed here programmatically). No deletion needed; this task is a verification, not a removal.
- [x] 2.2 Confirmed `block_order` already reads exactly: `title_iFe33P`, `variant_selector_pVYxPC`, `selling_plan_cKNXyy`, `buy_button_dT4wwt`, `description_EbxCM7`, `accordion_cMYdC4`, `accordion_9fTwmq`, `accordion_pNGji3` — no `upload_lift` entry to remove.
- [x] 2.3 No other block in this section, its `settings`, or its `name` were touched — verified byte-for-byte (semantically) identical to the pre-edit committed version via `git show HEAD:templates/product.json` comparison.

## 3. Add the reviews sections

Source: `../popon-shopify-store/templates/product.json` (veneers).

- [x] 3.1 Added `sections.customcode_content_generator_rUW68y`, copied verbatim from veneers ("MORE REVIEWS FROM POP ON" heading block).
- [x] 3.2 Added `sections.customode_loox_reviews_3WdAFq`, copied verbatim from veneers (Loox reviews carousel — `only_photos: false`, `hide_thumbnails: false`, `product_handle: ""`, `aggregated: true`, `data-limit: 4`, `maxwidth: 1080`).
- [x] 3.3 Confirmed veneers' two `_blocks` sections (`17601015186c120833` / Bundler, `17851703850d429cc0` / Amazon Reviews) were not ported — script asserted their absence before writing.

## 4. Update `order`

- [x] 4.1 Set `order` to: `["main", "customcode_featured_product_HRwfDi", "customcode_content_generator_rUW68y", "customode_loox_reviews_3WdAFq"]` — 4 entries, `main` and the hero unchanged in position, the two new sections appended at the end (matching where every other Energy PDP template places its Loox reviews section). Confirmed in the written file.

## 5. Verify

- [x] 5.1 Confirmed the file is valid JSON, `order` has exactly 4 entries, and every entry in `order` has a matching key in `sections` (no orphans either direction).
- [x] 5.2 Grepped the finished file for `shopify://apps/` — zero matches.
- [x] 5.3 Confirmed `sections.main` and `customcode_featured_product_HRwfDi` are semantically unchanged from the pre-edit committed version, via `git show HEAD:templates/product.json` comparison (parsed-and-compared, not raw-text — the file was re-serialized in full by the edit script, so JSON's `\/` vs `/` escaping for forward slashes in a couple of HTML strings differs cosmetically in the raw bytes; every parsed value is identical).
- [x] 5.4 Confirmed all 3 accordions remain `disabled: true`.
- [ ] 5.5 **Manual, in Shopify admin — not doable from this environment:** identify which live product(s), if any, currently use the default template (no explicit template override), and preview each one after this change ships to confirm the new reviews section renders and nothing else regressed. This step matters more here than in the Pro Pod / Ultra Clean Bundle changes, since this template can be live-affecting immediately.
