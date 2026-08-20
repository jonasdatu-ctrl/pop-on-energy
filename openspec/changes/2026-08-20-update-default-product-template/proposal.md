## Why

`templates/product.json` is the default PDP template — it applies to any Energy product that doesn't have an explicit template assigned, unlike the Pro Pod and Ultra Clean Bundle templates from the two prior changes, which each only affect one specific product. Unlike those two, this file already exists in Energy as a real, customized template (not a stub, not missing) — it just hasn't been kept in sync with veneers' version or, in one respect, with Energy's own other product templates.

Comparing against veneers' `product.json` and against Energy's own other product templates (`pop-on-clean`, `pop-on-fangs`, `whitening-starter-kit`, `pop-on-bling`, `pouch`) surfaced three gaps, confirmed during exploration:

1. Energy's default template is missing the reviews section (`customcode-content-generator` heading + `customode-loox-reviews` carousel) that every other Energy PDP template has at the bottom.
2. Veneers' version carries three app-block references — Amazon Reviews, Bundler, and Upload Lift — none of which appear anywhere else in the Energy repo, meaning none of those apps are installed on the Energy store.
3. Veneers' three trust accordions (terms/privacy/"Pop On Promise") are active; Energy's are currently disabled. **Explicitly confirmed during exploration to leave as-is** — even though every other Energy product template has these active, the client chose not to change this here, treating it as intentional for whatever falls back to this template rather than an oversight to fix.

Every section/snippet this template touches (`product-information`, `customcode-featured-product`, `_blocks`, `customcode-content-generator`, `customode-loox-reviews`) was already verified present and functionally equivalent between the two repos during the Pro Pod and Ultra Clean Bundle changes — no new `.liquid` file investigation was needed here.

## What Changes

- Append two sections to the end of Energy's `templates/product.json`, ported from veneers: `customcode-content-generator` ("MORE REVIEWS FROM POP ON" heading) and `customode-loox-reviews` (reviews carousel) — matching the pattern already used by every other Energy product template.
- Remove the Upload Lift app block (`upload_lift_app_block_B7z4ex`) from `customcode_featured_product_HRwfDi`'s `blocks`/`block_order` — this one is embedded inside the hero section's own block list, not a standalone section, so it requires editing that section's block map rather than dropping a whole section.
- Do not port veneers' two standalone `_blocks` app-block sections (Amazon Reviews, Bundler) — both dropped entirely, consistent with the Pro Pod and Ultra Clean Bundle precedent.
- Leave Energy's three existing accordions (`accordion_cMYdC4`/terms, `accordion_9fTwmq`/privacy, `accordion_pNGji3`/promise) exactly as they are — **still disabled**, not changed to match veneers or Energy's other templates. This was raised explicitly during exploration and the client chose to leave them as-is.
- Leave every other existing setting in Energy's `product.json` (title, variant_selector, selling_plan, buy_button, description block settings) completely untouched — this change only adds the two new sections and removes the one app block.
- No `.liquid` section, snippet, or block file changes.
- No image/video asset references exist in this template — nothing to blank.

## Capabilities

### New Capabilities
None.

### Modified Capabilities
None — this is theme content/configuration (one JSON template), not a product-facing requirement or spec change.

## Impact

- `templates/product.json`: modified in place. Sections grow from 2 to 4 (`main`, `customcode_featured_product_HRwfDi` [edited: one block removed], plus new `customcode-content-generator` and `customode-loox-reviews`).
- No other files change.
- **Blast radius note:** unlike the Pro Pod and Ultra Clean Bundle changes, this template is live-affecting — any product currently using the default template (no explicit template override) will pick up the new reviews section and lose the Upload Lift app-block reference immediately once this change ships. Which specific products that is isn't visible from the repo (store data, not theme code) — flagged for manual confirmation in Shopify admin before/after applying, same as the live-preview steps in the prior two changes.
- Accordions remain disabled — no behavior change there, called out explicitly so this isn't mistaken for an oversight later.
