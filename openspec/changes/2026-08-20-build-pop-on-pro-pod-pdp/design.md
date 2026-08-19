## Context

Energy's `templates/product.pop-on-pro-pod.json` today:

```json
{"sections":{"main":{"type":"product-information", ... ,"disabled":true, ...},
"customcode_content_generator_M7NEdk":{"type":"customcode-content-generator", ...}},
"order":["main","customcode_content_generator_M7NEdk"]}
```

Veneers' version of the same file has 24 section instances covering a full marketing PDP (see tasks.md for the ordered list). Both repos already follow the same live-PDP convention — confirmed by inspecting Energy's other built-out product templates (`product.pop-on-clean.json`, `product.pop-on-fangs.json`, `product.whitening-starter-kit.json`): the native `main` (`product-information`) section is always present but `disabled: true`, and a `customcode-featured-product` section is the section that actually renders the hero/buy-box. Veneers' pro-pod template follows the exact same pattern (`main` disabled, `customcode_featured_product_Dci89U` active), so porting it over is in-pattern for this theme, not a novel structure.

**File-parity check.** A first pass with `diff -rq` across `sections/`, `blocks/`, `snippets/` between the two repos showed nearly every shared file as "differs." Re-running with `--strip-trailing-cr` (Veneers' files are CRLF, Energy's are LF) collapsed almost all of that to zero — most files are byte-identical once line endings are normalized. Real, content-level differences that remained were checked individually against what this template actually uses:

| File | Real difference | Relevant to pro-pod? |
|---|---|---|
| `sections/customcode-featured-product.liquid` | Energy has one extra line: `data-product-id="{{ product.id }}"` | No — additive, not a regression |
| `snippets/customcode-general-variant-selector.liquid` | Veneers has an "Intelligems" A/B price-test block | No — gated behind `block.settings.display_price_diff`, which pro-pod sets `false` throughout |
| `snippets/customcode-buy-button.liquid` | Veneers renders blurb/payment-logo/footer-blurb markup inside the `enable_partially` (payment-plan) branch; Energy's version stubs that branch to a bare button | No — pro-pod sets `section.settings.enable_partially: false` on both `customcode-featured-product` sections. The normal ADD TO CART path (what pro-pod actually uses) is byte-identical in both repos, blurb/payment/footer-blurb rendering included |
| `snippets/customcode-styles.liquid`, `snippets/customcode-scripts.liquid` | Various — `c-sticky-scroll` CSS (belongs to `customcode-sticky-atc-scroll.liquid`, which pro-pod doesn't use), `enable_partially`-branch CSS, Intelligems CSS, a Klaviyo hook scoped to a different template, veneers' case-ID/GWP-freebie order-flow script | No — none of these code paths are reachable from pro-pod's settings |

Conclusion: no `.liquid` file needs to change for this template to render correctly. This change is scoped entirely to `templates/product.pop-on-pro-pod.json`.

## Goals / Non-Goals

**Goals:**
- Energy's Pro Pod PDP matches Veneers' Pro Pod PDP section-for-section, block-for-block, copy-for-copy.
- Remove the one app-integration block (Amazon Reviews grid widget) from the ported content.
- Leave every image/video slot empty so the client can upload and select Energy-specific assets without inheriting dangling references to files that only exist in Veneers' library.

**Non-Goals:**
- No new `.liquid` section, snippet, or block development — nothing is missing.
- No brand/copy rewriting. "Pop On Veneers" references are carried over verbatim (explicit decision, see Risks).
- No swap-in of Energy's existing reviews section (`customode-loox-reviews`) in place of the dropped app block — out of scope; the section is simply omitted.
- No changes to any other product template in this pass — scoped to `pop-on-pro-pod` only.

## Decisions

**1. Drop the Amazon Reviews app-block section entirely, don't keep an empty shell and don't swap in Loox.** Simplest option that satisfies "except app blocks" without introducing a decision (which reviews app, what settings) that belongs to a separate change.

**2. Copy all text verbatim, including veneers-specific brand references.** Matches the instruction to build this out "exactly as it is." Accepted as a known, deliberate mismatch — see Risks.

**3. Blank `image`/`video` settings rather than keep veneers' filenames.** Keeping veneers' filenames (e.g. `shopify://shop_images/Pro_Pod_for_website.png`) would silently work *if* the client later uploads a file with that exact name, but silently break/show nothing if they don't — same visible outcome as blanking, but with a hidden dependency on an exact filename match. Blanking is the more predictable default and matches "I'll do those" (the client will actively select assets in the editor, not rely on filename coincidence).

**4. No `.liquid` file changes.** See Context — every section/snippet dependency was verified content-equivalent for the code paths this template exercises.

## Risks / Trade-offs

- **Live copy will say "Pop On Veneers" on an Energy product page** until manually edited (grid-block header "Made for Pop On Veneers. Period.", FAQ answers referencing "Pop On Veneer materials," `alt_financing_link` pointing at `poponveneers.com/products/popon-payment-plans`). Deliberate per decision 2; flagged here and in proposal.md so it isn't mistaken for an oversight.
- **Every media slot renders empty** until the client uploads and selects assets — hero video, testimonial video, tabbed-content images, step-by-step images, trust-grid icons all show blank/placeholder in the interim.
- **Disabled sections/blocks are ported as-is, not pruned.** The disabled alt hero variant (`customcode_featured_product_Q7KjCd`), disabled alt copy blocks, and disabled trust/tech-tab variants add editor clutter but preserve veneers' A/B-test and legacy-copy history in case any of it is wanted later. Trade-off: a noisier template vs. losing that history; kept per "exactly as it is."
