## Context

Energy's current `templates/product.json`:

```json
{"sections":{
  "main": {..., "disabled": true},                          // native fallback
  "customcode_featured_product_HRwfDi": {...}                // hero: title, variant_selector,
                                                               // selling_plan, buy_button, description,
                                                               // 3 accordions (all disabled)
},
"order": ["main", "customcode_featured_product_HRwfDi"]}
```

Veneers' version of the same file:

```json
{"sections":{
  "main": {..., "disabled": true},
  "customcode_featured_product_HRwfDi": {...},                // same hero blocks PLUS an
                                                                // upload_lift app block, PLUS
                                                                // 3 accordions all ACTIVE
  "17601015186c120833": {"type": "_blocks", ...},              // Bundler app block
  "17851703850d429cc0": {"type": "_blocks", ...},              // Amazon Reviews app block
  "customcode_content_generator_rUW68y": {...},                // "MORE REVIEWS FROM POP ON"
  "customode_loox_reviews_3WdAFq": {...}                       // Loox reviews carousel
},
"order": ["main","customcode_featured_product_HRwfDi","17601015186c120833","17851703850d429cc0","customcode_content_generator_rUW68y","customode_loox_reviews_3WdAFq"]}
```

**App-block installation check.** Grepped the entire Energy repo for `apps/bundler`, `apps/upload-lift`, and `apps/amazon-reviews` — zero matches anywhere outside this one file's veneers counterpart. None of these three apps appear to be installed on the Energy store. This is the same conclusion reached for Amazon Reviews in both prior changes, now confirmed to extend to Bundler and Upload Lift too.

**Accordion-state check.** Compared the terms/privacy/promise-style accordions across every other Energy product template that has them: `pop-on-clean`, `pop-on-fangs`, `whitening-starter-kit`, `pop-on-bling`, `pouch` — all active (`disabled` absent or `false`). Only the default `product.json` has them disabled. The `termsandconditions`/`privacypolicy`/`promise` page handles they link to are referenced across eight Energy templates, confirming those pages exist and this is a live, used pattern elsewhere. Raised this explicitly during exploration as looking like an oversight — **the client's call was to leave it as-is**, so this change does not touch accordion state, despite the apparent inconsistency with the rest of the theme.

**File-parity.** No new investigation needed — `product-information`, `customcode-featured-product`, `_blocks`, `customcode-content-generator`, and `customode-loox-reviews` were all already verified present and content-equivalent between the two repos during the Pro Pod and Ultra Clean Bundle changes. `description` block type (new to this template, not seen in the prior two) renders inline inside `customcode-featured-product.liquid`, which was already confirmed identical.

**No asset references.** Unlike Pro Pod and Ultra Clean Bundle, veneers' `product.json` has zero `shopify://shop_images/` or `shopify://files/videos/` values anywhere — nothing to blank in this change.

## Goals / Non-Goals

**Goals:**
- Bring Energy's default template's reviews section up to par with every other Energy PDP template (Loox carousel + heading).
- Remove all three app-block references, none of which correspond to an app actually installed on Energy.
- Change nothing else — this is the most conservative of the three changes so far, given the default template's broader blast radius.

**Non-Goals:**
- Not enabling the disabled accordions — explicit client decision, despite the inconsistency with the rest of the theme. Not revisited in this change.
- Not touching `title`, `variant_selector`, `selling_plan`, `buy_button`, or `description` block settings — these are Energy's own existing configuration, untouched.
- Not identifying which live products currently use this default template — that's Shopify admin/store data, outside this change and outside the repo entirely.
- No new `.liquid` file changes.

## Decisions

**1. Drop all three app blocks, including Upload Lift, with no special-casing.** Exploration initially flagged Upload Lift as worth a second look (it plausibly ties to a custom-fit/photo-upload workflow specific to veneers), but confirmed via grep that it doesn't appear anywhere else in the Energy repo either — same conclusion as the other two. Explicit decision: drop all three uniformly, consistent with precedent, rather than carve out an exception.

**2. Upload Lift removal is a block-level edit, not a section drop.** Unlike Bundler and Amazon Reviews (each their own standalone `_blocks` section, dropped wholesale — same mechanic as the Pro Pod and Ultra Clean Bundle changes), Upload Lift is one block inside `customcode_featured_product_HRwfDi`'s own `blocks` map and `block_order` array. Removing it means deleting that one block entry and its `block_order` reference, while leaving every sibling block (`title`, `variant_selector`, `selling_plan`, `buy_button`, `description`, and the 3 accordions) and the section's own settings completely untouched.

**3. Leave the accordions disabled.** Explicit client decision during exploration, overriding the "enable to match the rest of the theme" recommendation. Documented here so it reads as a deliberate choice, not something missed.

**4. Append the two new sections at the end of `order`, matching veneers' position** (after the hero, where the dropped app-block sections used to sit) — consistent with where every other Energy PDP template places its Loox reviews section (last).

## Risks / Trade-offs

- **This is the default template — changes here can be live-affecting immediately**, unlike the Pro Pod and Ultra Clean Bundle changes, which only affected a not-yet-live product each. Exactly which products currently fall back to this template isn't visible from the repo. Flagged for manual confirmation in Shopify admin; the live-preview verification step in tasks.md matters more here than it did for the previous two changes.
- **The accordion-state inconsistency remains unresolved.** Every other Energy PDP template has these active; the default template alone does not, and this change deliberately leaves that as-is per the client's explicit call. If this turns out to have been an oversight rather than intentional, it's a one-line `disabled` flag fix later, not part of this change.
- **Removing the Upload Lift block assumes it's genuinely unused on Energy.** The grep-based check (zero references elsewhere in the repo) is strong evidence but not a guarantee the app isn't installed and simply unused elsewhere in the *theme* while still active in the *Shopify admin* app list. If it turns out the app is installed, its block reference would need to be re-added — low risk, but worth knowing this wasn't checked against the Shopify admin's installed-apps list, only against the repo.
