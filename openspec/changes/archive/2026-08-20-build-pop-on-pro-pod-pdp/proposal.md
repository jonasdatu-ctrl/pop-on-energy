## Why

`templates/product.pop-on-pro-pod.json` in Pop On Energy is currently a near-empty stub — just the disabled default `product-information` section plus one ATC-scroll JS hook. Its sibling theme, Pop On Veneers (`popon-shopify-store`), has a fully built-out Pro Pod PDP: hero/buy-box, video intro, video testimonial, a tabbed "how it works" explainer, a "3 Easy Steps" walkthrough, trust-badge grids, an FAQ accordion, and a sticky add-to-cart bar — 24 section instances in total.

Every `.liquid` section and snippet this template depends on was confirmed present and functionally equivalent in Energy already, via a full content diff (not just filenames — `diff -rq` alone was misleading because it flagged near-universal "differs" that turned out to be pure CRLF-vs-LF line-ending noise). So this is a **content/config port**, not new section development: copy the veneers template's section/block structure and settings into Energy's stub, minus the one app-integration block and minus image/video asset references (client uploads/selects those separately).

(Correction from initial exploration: veneers' template actually has 25 section instances, not 24 as first estimated — confirmed by parsing the file programmatically during apply.)

## What Changes

- Rewrite `templates/product.pop-on-pro-pod.json` in the Energy repo to mirror `templates/product.pop-on-pro-pod.json` from the Veneers repo, porting all section instances, their block trees, settings, `custom_css`, `name`, and `disabled`/enabled flags as-is (including veneers' disabled A/B-test hero variant and other disabled legacy blocks — nothing pruned beyond what's listed below).
- Drop section `1785169794dfa76500` — a `_blocks` container whose only content is a `shopify://apps/amazon-reviews/...` grid-review-widget app block. This is the one genuine app block in the template. It is not ported and not replaced (energy's other PDPs use a different, non-app-block reviews section — swapping one in is out of scope for this change).
- Blank every image/video asset-reference setting (`image` / `video` keys pointing at `shopify://shop_images/...` or `shopify://files/videos/...`) to empty strings across all ported sections/blocks. All other settings on those same blocks (aspect ratio, autoplay, loop, layout, etc.) are kept as-is.
- All copy is ported verbatim — headings, body text, FAQ answers, CTA labels, and the `alt_financing_link` pointing at `poponveneers.com` — including references to "Pop On Veneers." No brand/product-line rewriting in this change (explicit decision; see design.md Risks).
- No `.liquid` section, snippet, or block file changes. Confirmed via full content diff that everything this template renders through is already present and equivalent between the two repos.

## Capabilities

### New Capabilities
None.

### Modified Capabilities
None — this is theme content/configuration (a single JSON template), not a product-facing requirement or spec change.

## Impact

- `templates/product.pop-on-pro-pod.json`: full rewrite, 24 section instances (veneers' 25, minus the dropped app-block section).
- No other files change.
- Once applied, visiting the Pro Pod PDP in Energy will show the same section structure, order, and copy as Veneers' Pro Pod PDP, with all image/video slots empty until the client uploads matching assets in Energy's admin and re-selects them in the theme editor.
- Known accepted issue, carried over deliberately: ported copy references "Pop On Veneers" (e.g. "Made for Pop On Veneers. Period.", FAQ answers about "Pop On Veneer materials," the poponveneers.com financing link) on what is an Energy product page. A follow-up copy pass is expected separately, outside this change.
