# Web Research for Validation

How to run the demand and coverage checks and turn them into a `market_reality` verdict. Keep it fast: a handful of searches, the strongest evidence, a clear call. You are looking for signal, not writing a literature review.

Tools: `WebSearch` and `WebFetch` for the web, plus the read-only GapQuery tools (`category-overview`, `find-disruption-targets`, `search-apps`) for the in-marketplace side. Start from the workflow or pain phrase, not the opportunity's marketing name.

## Demand check (does anyone actually do this?)
Run 3-5 searches, varying the angle. Templates (replace [workflow] with the real phrase):
- `[workflow] reddit`
- `how do I [workflow]`
- `[workflow] is there a tool` or `[workflow] app recommendation`
- `[workflow] frustrating` / `[workflow] manually` / `[workflow] spreadsheet`
- `[platform] [workflow]` (e.g. "shopify bundle subscription")

Then `WebFetch` the 2-3 most promising threads (Reddit, indie forums, Stack Exchange, niche communities, blog comment sections) and pull verbatim quotes of people describing the pain or the manual workaround.

Verdict:
- **confirmed**: multiple independent people describe this workflow or pain in their own words, recently enough to matter.
- **weak**: a little chatter, or only old or thin mentions.
- **none**: you searched honestly and found essentially nobody talking about it. Say so; this is the most useful negative result.

## Coverage check (is the gap real outside the marketplace?)
The marketplace may be thin while the wider market is not. Run 2-4 searches for what already exists outside the app store:
- `[workflow] software` / `[workflow] SaaS` / `best [workflow] tool`
- `[workflow] zapier` / `[workflow] automation`
- `[incumbent or category] alternative`

`WebFetch` the top contenders to see how well they actually solve it. Cross-reference the in-marketplace side: use `category-overview` for how thin the category is, `find-disruption-targets` for weak incumbents, `search-apps` for what is in the marketplace already.

Verdict:
- **open**: no standalone tool really does this; the gap holds in the wider market too.
- **partial**: some tools touch it, but poorly, expensively, or without the integration that makes the marketplace version valuable. There is still room.
- **covered_outside_marketplace**: a mature standalone tool (or Zapier recipe, or a widely-shared template) already does this well. The gap is only inside the marketplace. This is the yellow flag; surface it plainly.

## Mapping to save-market-reality
Call `save-market-reality` with:

```jsonc
{
  "opportunity_slug": "shopify-bundle-subscription-bridge",
  "market_reality": {
    "demand_verdict": "confirmed",       // confirmed | weak | none
    "coverage_verdict": "partial",       // open | partial | covered_outside_marketplace
    "summary": "Two to three plain sentences: what the web shows about demand and coverage, and what it means for the decision.",
    "signals": [
      { "source": "reddit", "url": "https://reddit.com/r/shopify/...", "quote": "verbatim user words", "type": "demand" },
      { "source": "g2", "url": "https://g2.com/...", "quote": "verbatim", "type": "coverage" }
    ]
    // checked_at is set automatically if you omit it
  }
}
```

Rules:
- Keep `signals` to the strongest 2-5. One great quote beats five weak ones.
- `quote` is verbatim. Do not paraphrase or clean it up; it is evidence.
- `source` is a short label (`reddit`, `forum`, `blog`, `g2`, `competitor_site`, `other`).
- `type` is `demand` (evidence people want it) or `coverage` (evidence something already does it).
- The `summary` follows `voice.md`: no em dashes, plain words, lead with the verdict.

## Scope guard
This skill stops at the verdict. Do not run the 6-area build/skip study here; that is `gapquery-research`, which reads this `market_reality` and builds on it.
