## Why

Pop On Veneers (`popon-shopify-store`, the sibling theme) has a built-out PDP for `templates/product.ultra-clean-bundle.json` — a subscription bundle page (30-day cleaning tablets + foam + brush + storage case) with a featured-product hero, bundle-detail/shipping/warranty accordions, a "what's included free" grid, and a Loox reviews section. Pop On Energy has **no file at all** at that path — unlike the Pro Pod template (which existed as a disabled stub), this template doesn't exist yet in Energy.

Every section/snippet this template renders through was confirmed present and functionally equivalent in Energy via full content diff (matching the same verification approach used for the Pro Pod change): `customcode-featured-product`, `_blocks`, `product-information`, `customcode-content-generator`, `customode-loox-reviews`, and the snippets `customcode-freebies.liquid` / `customcode-selling-plan.liquid` are all byte-identical between the two repos. `customcode-accordion.liquid` genuinely differs (veneers supports `above_content` and `custom_html`/`custom_liquid` extras energy's version lacks), but none of this template's 5 accordion blocks populate those settings — dead code either way, not a blocker.

This template's shape is also the closest match yet to an existing Energy convention: `templates/product.pop-on-clean.json` (Energy's own live "Pop On Clean" tablets PDP) already uses the identical section skeleton — `main`[disabled] → `customcode-featured-product` (title/variant_selector/selling_plan/buy_button/accordion blocks) → `_blocks` → `customode-loox-reviews`. Porting this template is squarely in-pattern.

So this is, again, a **content-creation task** (write a new template JSON file), not new section/snippet development.

## What Changes

- Create `templates/product.ultra-clean-bundle.json` in the Energy repo, porting veneers' 5 section instances (`customcode-featured-product`, the app-block `_blocks` container, `main`, `customcode-content-generator`, `customode-loox-reviews`) with their block trees, settings, and `disabled` flags, minus the one exception below.
- Drop the `_blocks` container whose only content is the `shopify://apps/amazon-reviews/...` grid-review-widget app block — same treatment as the Pro Pod change, kept consistent rather than following energy's alternate "keep an empty shell" pattern (an explicit choice; see design.md).
- Blank every image/video asset-reference setting (any key — `image`, `image_one`..`image_four`, `video` — whose value points at `shopify://shop_images/...` or `shopify://files/videos/...`) to empty strings — same policy as the Pro Pod change, matched by value rather than by a fixed key name since this template's `freebies` block uses `image_one`/`image_two`/`image_three`/`image_four` instead of a plain `image` key.
- All copy is ported verbatim, including the warranty accordion's "...ensures you love your Pop On Veneers" line — consistent with the Pro Pod change's decision to copy verbatim rather than rewrite brand references in this pass.
- No `.liquid` section, snippet, or block file changes.

## Capabilities

### New Capabilities
None.

### Modified Capabilities
None — this is theme content/configuration (a single new JSON template), not a product-facing requirement or spec change.

## Impact

- `templates/product.ultra-clean-bundle.json`: new file, 4 section instances (veneers' 5, minus the dropped app-block section).
- No other files change.
- Once applied, this template exists in the theme and can be assigned to a product in Energy's admin. **Note (not part of this change):** no product with a matching handle is known to exist in Energy's catalog yet — creating/assigning that product is a store-data action outside theme code, and outside this change's scope.
- Known accepted issue, carried over deliberately: the warranty accordion says "Pop On Veneers" on what would be an Energy product page. Flagged here per the same pattern as the Pro Pod change.
- Known snippet-level gap, not exercised by this template: `snippets/customcode-accordion.liquid` in Energy lacks veneers' `above_content` and `custom_html`/`custom_liquid` rendering branches. If a future edit in Shopify's admin sets those fields on this template's accordion blocks, they won't render until that snippet is updated — out of scope here since none of the 5 ported accordion blocks currently use them.
