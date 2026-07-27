# Dick's Pawn Superstore — AI Proposal Deck

Pitch deck for the Dick's Pawn Superstore CEO. One combined deck, 18 slides,
self-contained HTML: the business case, how the redesign installs as their
Shopify theme, the five Claude systems that write drafts into the store, the
pawn-counter integration, the ROI calculator, guardrails, plan and pricing.

**Live:** https://kylefriesmarketing.github.io/dicks-pawn-pitch/
**PDF copy:** `DicksPawn-AI-Proposal.pdf` (18 pages, same content)
**Implementation detail:** `SHOPIFY-CLAUDE-PLAN.md` (yours, not for the CEO)

## Using it
- **Present:** arrow keys / space / swipe. `P` prints.
- **PDF:** open, press `P`, choose "Save as PDF", set **Landscape** and
  **Background graphics ON** (otherwise the dark slides print white).
- **Deep link:** `#s12` jumps to slide 12 (the calculator).

## Before you send it
1. Add the CEO's name to the cover (currently addressed to the company).
2. Add your phone number on the last slide.
3. Confirm you're happy with the pricing on slide 17 — those are starting
   positions, not fixed rates.

## Where the numbers come from
Everything stated about their business is measured, not estimated:

| Claim | Source |
|---|---|
| 1,565 items / 1,527 in stock / category split | Their public Shopify product feed, 26 Jul 2026 |
| 94.7 KB vs 7.7 KB homepage, 0.42s vs 0.15s TTFB | `curl --compressed`, both sites, same connection |
| No homepage structured data | Zero `ld+json` blocks on dickspawn.com home |
| No per-city pages | `/pages/conway` etc. return 404 |
| Product pages already correct | They carry Product + BreadcrumbList schema |
| 12 SKU formats ending in staff initials | Their product feed — the hand-keying evidence on slide 11 |
| 4.9 stars / 2,000+ reviews / 5 stores / since 1987 | Their own site and listings |

Slide 12 is a live calculator — the CEO's inputs, not assumed figures. The
formula is deliberately plain (items × minutes saved × wage) so it can be
checked on paper.

## Repo note
This repo is public so the link works without a login. It carries pricing,
so make the repo private (Settings → Danger Zone) if you'd rather it not be
reachable by URL — Pages will stop serving it on a free plan.
