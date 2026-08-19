## Context

Energy has no `templates/product.ultra-clean-bundle.json` at all today — not even a disabled stub (unlike Pro Pod, which existed as a stub before this line of changes started). Veneers' version:

```json
{"sections":{
  "customcode_featured_product_Dci89U": {...},   // hero/buy-box, active
  "178517016081f46b69": {...},                    // _blocks: Amazon Reviews app block
  "main": {..., "disabled": true},                // native fallback
  "customcode_content_generator_J6dJht": {...},   // "MORE REVIEWS FROM POP ON" heading
  "customode_loox_reviews_nyGgWe": {...}          // Loox reviews carousel
},
"order": ["customcode_featured_product_Dci89U","178517016081f46b69","main","customcode_content_generator_J6dJht","customode_loox_reviews_nyGgWe"]}
```

**This shape already matches an existing, live Energy template.** `templates/product.pop-on-clean.json` — Energy's own "Pop On Clean" tablets PDP — uses the identical skeleton: `main`[disabled] → `customcode-featured-product` (with `title`/`variant_selector`/`selling_plan`/`buy_button`/`accordion` blocks) → `_blocks` → `customode-loox-reviews`. Energy's `_blocks` container there is present but empty — no app block was ever put in it. This is a second, independent confirmation (beyond Pro Pod) that this section skeleton is the theme's standard PDP pattern, not something specific to veneers.

**File-parity check**, same method as the Pro Pod change (content diff with `--strip-trailing-cr` to remove line-ending noise, not just `diff -rq` filenames):

| File | Real difference | Relevant to ultra-clean-bundle? |
|---|---|---|
| `sections/customcode-featured-product.liquid` | Energy has one extra line (`data-product-id`) | No — additive, already confirmed in the Pro Pod change |
| `snippets/customcode-freebies.liquid` | none | — identical |
| `snippets/customcode-selling-plan.liquid` | none | — identical |
| `sections/customode-loox-reviews.liquid` | none | — identical |
| `snippets/customcode-accordion.liquid` | Veneers renders an `above_content` block above the trigger, and `custom_html`/`custom_liquid` free-form content inside it; Energy's version doesn't | No — none of this template's 5 accordion blocks set `above_content` or `custom_liquid`, and `custom_html` is present but blank (`""`) on every one. The `{% if block.settings.custom_html != blank %}` guard means it wouldn't render in veneers either, given blank input |

Conclusion, same as Pro Pod: no `.liquid` file needs to change for this template to render correctly. Scoped entirely to a new `templates/product.ultra-clean-bundle.json`.

**Brand-copy footprint is much smaller than Pro Pod's.** Scanning every block: `title_RLTGBX` ("Ultra Clean Bundle™"), `freebies_HLBndF` (cleaning tablets / foam / case / toothbrush), and the active "Bundle Details" / "Shipping Information" accordions are all product-line neutral — no veneers-specific claims. The only active mention is in `accordion_dtehUe` ("Description"): *"Our 30-day 'Pop On Promise Warranty' ensures you love your Pop On Veneers."* Two other accordions that do reference veneers explicitly (`accordion_JjE8aG` "See How Your Veneers Are Made", `accordion_Wxj6yq` "Around NYC?", with a `poponveneers.com` link) are both `disabled: true` in the source — inert either way.

## Goals / Non-Goals

**Goals:**
- Create Energy's `templates/product.ultra-clean-bundle.json` matching veneers' section/block structure and copy.
- Stay consistent with the precedent set by the Pro Pod change for app blocks, brand copy, and asset references, rather than re-litigating each one per template.

**Non-Goals:**
- No new `.liquid` section/snippet/block development — nothing is missing for what this template actually renders.
- No fix to `snippets/customcode-accordion.liquid`'s `above_content`/`custom_html`/`custom_liquid` gap — noted as a known limitation, not exercised by this template, out of scope here.
- No creation of a matching product in Energy's admin, and no confirmation that one is planned — that's store data, not theme code, and outside this change.
- No brand-copy rewriting.

## Decisions

**1. Drop the Amazon Reviews app-block section entirely, not an empty shell.** Energy's own `pop-on-clean.json` keeps that same `_blocks` container as an empty shell rather than removing it, which was raised as an alternative during exploration. Explicit decision: stay consistent with the Pro Pod precedent (drop entirely) rather than branch behavior per template. Revisit as a deliberate, single decision later if the empty-shell convention is preferred going forward.

**2. Copy all text verbatim, including the "Pop On Veneers" warranty line.** Consistent with the Pro Pod change's decision 2. Same accepted trade-off — see Risks.

**3. Blank every asset-reference value (`shopify://shop_images/...` / `shopify://files/videos/...`), matched by value not key name.** Same rationale as Pro Pod decision 3 — predictable default, no hidden dependency on veneers' exact filenames. Unlike Pro Pod, this template's `freebies` block uses non-standard key names (`image_one`..`image_four`), so the transform must match by URI scheme/prefix, not by assuming the key is literally called `image` or `video`. One video reference exists too, inside the disabled `accordion_JjE8aG` block — blanked the same way despite the block being disabled, for consistency with how Pro Pod's disabled-block image references were still blanked.

**4. No `.liquid` file changes**, including not backporting `customcode-accordion.liquid`'s `above_content`/`custom_html`/`custom_liquid` support. It's unused by every block in this specific template; fixing it now would be scope creep unrelated to building this PDP.

## Risks / Trade-offs

- **The warranty accordion says "Pop On Veneers" on an Energy product page** until manually edited. Smaller footprint than Pro Pod (one line vs. many), same deliberate trade-off.
- **No product exists yet to attach this template to**, as far as this repo can tell. If that assumption is wrong (or a product creation was expected as part of this work), the template will sit unused until someone assigns it in Shopify admin.
- **`customcode-accordion.liquid`'s `above_content`/`custom_html`/`custom_liquid` gap remains unfixed.** If a future content edit in Shopify's admin tries to use those fields on this template's accordion blocks, they'll silently not render in Energy until the snippet is updated to match veneers'. Not a risk today (all 5 blocks leave those fields unset/blank), but worth remembering if this surfaces later.
