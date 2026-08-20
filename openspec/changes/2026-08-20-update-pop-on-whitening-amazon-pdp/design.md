## Context

Energy's current `templates/product.pop-on-whitening-amazon.json`:

```
order: [
  customcode_featured_product_Dci89U,   // hero
  1768322716829cb631,                    // _blocks: 13 ai_gen_block image blocks
  main,                                  // [disabled] native fallback
  customcode_content_generator_Qk9iPc,   // [disabled]
  customcode_content_generator_GYfgzK,   // "What are customers saying?" heading
  customode_loox_reviews_NUXYAQ          // Loox reviews carousel
]
```

Veneers' version:

```
order: [
  customcode_featured_product_Dci89U,    // hero
  customcode_content_generator_tn8qXi,   // "Trending On TikTok" — MISSING in energy
  1768322716829cb631,                     // _blocks: same 13 ai_gen_block image blocks
  main,
  customcode_content_generator_Qk9iPc,
  1785170463ff170f29,                     // _blocks: Amazon Reviews app block — MISSING in energy
  customcode_content_generator_GYfgzK,    // "MORE REVIEWS FROM POP ON" heading
  customode_loox_reviews_NUXYAQ
]
```

**Section-by-section identity check**, run against every section ID present in Energy's file:

| Section | Identical to veneers? | Notes |
|---|---|---|
| `main` | Yes | — |
| `customcode_content_generator_Qk9iPc` | Yes | disabled in both |
| `customode_loox_reviews_NUXYAQ` | Yes | — |
| `1768322716829cb631` (image gallery) | **No** | All 13 `ai_gen_block_*` image blocks are identical (same images, same order); only the container's own `settings` differ — veneers applies `color_scheme: "scheme-6"` and `padding-block-start: 20`, energy uses the default scheme and no top padding. Cosmetic layout only. |
| `customcode_featured_product_Dci89U` (hero) | **No** | See below — this is where the real divergence is |
| `customcode_content_generator_GYfgzK` | **No** | Heading text itself differs: veneers `<h3><strong>MORE REVIEWS FROM POP ON</strong></h3>`, energy `<h2><strong>What are customers saying?</strong></h2>` |

**Hero section divergence, in detail** (`customcode_featured_product_Dci89U`):

- `accordion_ByhxAr` ("Product Details") — energy's bullet copy has been substantively rewritten, not just reworded. Veneers' 5th bullet: *"EASY, NON-SLIP APPLICATION — The dry-strip technology keeps strips in place..."* Energy's 5th bullet: *"CLINICALLY PROVEN RESULTS WITHOUT SENSITIVITY — Backed by clinical testing..."* — a different product claim entirely, not a paraphrase. Every other bullet headline is also reworded (e.g. "PEROXIDE-FREE W/ ALL-NATURAL & GENTLE FORMULA" → "PEROXIDE-FREE, CLEAN FORMULA"), and "whiter teeth in just days" → "in as little as 7 days."
- `accordion_dtehUe` — retitled "Pop On Promise" → "Description"; body text shortened (drops the "Free Shipping on orders above $29" sentence present in veneers).
- `accordion_wjdWUJ` ("Shipping Information") — **enabled in energy** (`disabled` key absent), **disabled in veneers** (`disabled: true`). This is the opposite direction from the accordion-state difference found in the default-template change (there, veneers was more active than energy; here, energy is more active than veneers).
- `variant_selector_3HH3r4`, `buy_button_mxEHNA`, `accordion_JjE8aG`, `accordion_Wxj6yq` — differ only in trivial ways already established as inert in prior changes: JSON key ordering (`title_placement` position), the empty-string `custom_html` key's presence/absence (ties to the known `customcode-accordion.liquid` snippet gap), and one settings key (`display_compare_price`) present in veneers but absent in energy — absence falls back to the block schema's default, not a functional difference given both templates render this same block type via the same, already-verified-identical `customcode-featured-product.liquid`.
- `title_RLTGBX`, `blurb_text_riijCz`, `blurb_text_GDUcYD`, `callout_XCNXFf`, `selling_plan_Mn3WFR`, `freebies_HLBndF` — not individually re-verified line-by-line here since they didn't surface in the top-level identity check as containing the section's real differences, but the section-level diff confirmed the *only* semantically real divergences are the four bullet points above.

**Conclusion: this is not a "energy is behind veneers" situation for the overlapping sections.** It's independent parallel content work. The two genuinely missing pieces (`customcode_content_generator_tn8qXi`, and the second `_blocks`/Amazon-Reviews section) are real gaps; everything else is deliberate divergence to be preserved.

**Firework/TikTok integration check.** `fw-embed-feed` and `channel="pop_on"` already appear in Energy's own `templates/index.json` and as the default in `sections/customcode-text-marquee.liquid` — confirmed via grep. This is a shared "Pop On" umbrella integration already live on Energy, not something veneers-exclusive being introduced for the first time.

**No `.liquid` file changes needed.** `ai_gen_block_fe68796.liquid` exists in Energy's `blocks/` folder already (confirmed). `customcode-content-generator.liquid` and `customcode-featured-product.liquid` were both already verified identical/equivalent between the two repos in the Pro Pod and Ultra Clean Bundle changes.

## Goals / Non-Goals

**Goals:**
- Add the one missing content section (Trending On TikTok).
- Drop the one app-block section (Amazon Reviews), consistent with every prior change in this line of work.
- Leave every section that already exists in Energy completely untouched, preserving its independently-written copy.

**Non-Goals:**
- Not reconciling the image-gallery section's cosmetic styling difference (`color_scheme`, `padding-block-start`) — explicitly raised during exploration and the client chose to leave it alone, scoping this change to only the two clean gaps.
- Not "fixing" any of the hero section's content to match veneers — the whole point of this change is the opposite: preserve Energy's own copy.
- Not resolving the `accordion_wjdWUJ` enabled/disabled inconsistency with veneers (or with the default template's opposite-direction inconsistency) — out of scope, not touched.
- No new `.liquid` file development.

## Decisions

**1. Scope this change to exactly the two structural gaps, nothing else.** The client's explicit choice during exploration, given that every overlapping section already contains real, independent Energy-specific content. This is the narrowest of the four changes in this line of work.

**2. Drop the Amazon Reviews app-block section entirely**, not an empty shell — consistent with the Pro Pod, Ultra Clean Bundle, and default-template precedent. Not re-litigated here since the client already made this call three times.

**3. Port the Trending On TikTok section verbatim, including its `channel="pop_on"` value**, since that channel is already Energy's own established usage elsewhere in the theme — no adaptation needed, unlike the brand-copy questions raised on the Pro Pod and Ultra Clean Bundle changes.

**4. Insert the new section at veneers' relative position** (immediately after the hero) rather than appending it elsewhere, to match the visual flow veneers already validated.

## Risks / Trade-offs

- **The image-gallery section's minor styling difference (`color_scheme: "scheme-6"`, 20px top padding in veneers vs. defaults in energy) remains unreconciled.** Cosmetic only — same images, same blocks, same order — left alone per explicit scope decision. Trivial to apply later as a one-line settings tweak if wanted.
- **This change locks in Energy's current copy as correct-and-final for this product**, since nothing here compares it against any other source of truth (e.g., whether the rewritten bullets were reviewed/approved) — that's outside what a repo diff can tell us. Flagged so this isn't mistaken for a content-QA pass.
