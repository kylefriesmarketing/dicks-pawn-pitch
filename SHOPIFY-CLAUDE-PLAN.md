# Dick's Pawn — Shopify-Native Rebuild + Claude AI Layer

Implementation plan, revised now that the target is their **existing Shopify store**
(dickspawn.com) with **Claude** powering the AI systems. Supersedes the standalone-site
assumptions in the earlier notes; the pitch deck's promises are unchanged — this is how
they actually get delivered.

---

## 1. The architecture decision: become the theme

Their store already runs Shopify: checkout, cart, the Call-for-Price app, One AI SMS,
their eBay flow, and product pages that already carry correct `Product` schema. The
rebuild should not sit *next to* that — it should *become* the storefront.

**Decision: port the redesign as a Shopify Online Store 2.0 theme** (Liquid + JSON
templates) on their existing store.

| Option | Verdict | Why |
|---|---|---|
| **A. New design as their Shopify theme** | ✅ **Do this** | Checkout, apps, eBay, domain authority all untouched. The site *is* the store, so sold = gone automatically — the sync problem vanishes by construction. Reversible: publish as an unpublished theme, preview, flip when approved, old theme kept. |
| B. Headless storefront (Storefront API + our own front end) | ❌ Rejected | More infra, checkout handoff friction, SEO migration risk, and it re-creates the sync problem we just eliminated. Over-engineering for this business. |
| C. Keep the GitHub Pages site as a separate marketing layer | ❌ Rejected | Two sites competing for the same queries = duplicate content. The Pages build was the *proof*, not the product. |

### What survives from the rebuild (most of it)

| Asset | Where it goes |
|---|---|
| Design system (`style.css`, depth tokens, SVG icon set) | Theme assets, ported as-is |
| Homepage layout (hero with live deals, action strip, photo category tiles) | `index.json` template + sections; "live deals" become real product references — no JS catalog needed |
| Quick-view modal | Theme JS, fed by same-origin `/products/<handle>.js` (Shopify serves per-product JSON natively — `details.json` retired) |
| Category browsing + jewelry subfilters | Native **collections** built from the normalized tags (see §3.2) + Shopify's storefront filtering; our custom browser JS retired |
| 5 city landing pages | Shopify **Pages** with a custom `page.location` template; LocalBusiness/PawnShop schema moves into the theme |
| Homepage/org structured data, OG tags, share card | `theme.liquid` + settings; `og-card.jpg` uploaded as a theme asset |
| Mobile nav, sell/quote page, services/about content | Theme sections/templates |
| **The decoded tag taxonomy** (`HW`=handbags, `SH`=speakers, GR/AP/… codes) | Becomes the brain of the Catalog Normalizer (§3.2) — this is the single most valuable artifact from the build |

### What gets retired

- The 1,565 static `/p/` pages — their real product pages already exist with correct
  schema on the domain Google already trusts; ours would be duplicate content. The
  *design* of our product page ports to `product.json` as a restyle.
- The nightly scrape/regenerate pipeline (`gen-products.mjs`, `gen-pages.mjs`) as a
  site-feeding mechanism — the theme reads live data. The classification logic inside
  it is promoted into the normalizer.
- The GitHub Pages site itself, once the theme is live — keep the repo as the design
  reference and staging preview.

### Deployment mechanics

- Shopify CLI (`shopify theme dev`) for live-preview development against their store;
  `shopify theme push` to an **unpublished** theme; the CEO previews at a private URL;
  publish is one click and one-click reversible.
- **Access needed:** a collaborator account with *Themes* and *Products* scopes. This is
  the one concrete ask from the CEO to start (see §6).

---

## 2. Where Claude fits — the shape of every system

All five AI systems share one pattern, which keeps them cheap, safe, and consistent
with the deck's guardrails slide:

```
Trigger (photo upload / webhook / cron)
   → Claude API call (Messages API, structured outputs)
   → DRAFT written into a system the staff already use
   → Human approves in that system
   → Published
```

The critical trick: **Shopify draft products are the human gate.** A product created
via the Admin API with `status: "draft"` is invisible to customers until someone on
staff — in the Shopify admin they already log into every day — reviews and activates
it. We never build a custom review UI, and nothing reaches a customer unread. That's
the deck's "human gate" promise implemented with zero new software for staff to learn.

**Data boundary (compliance story):** Claude only ever sees *merchandise* — photos,
titles, item numbers, prices. The pawn system's loans, customer identities, and
reporting obligations are never in any prompt. This is exactly the guardrail slide,
made concrete.

**Model policy:** `claude-opus-4-8` for anything customer-facing (listings, review
replies, chat) — quality is the product. The one-time catalog normalization runs
through the **Batch API** (50% discount, results within hours — perfect for a
non-interactive backfill).

---

## 3. The five Claude systems

### 3.1 Listing Assistant — the pilot ($1,500 scope)

The hand-keying gap is proven: their SKUs show 12+ formats ending in staff initials —
at least six people typing items from the pawn system into Shopify by hand.

**Flow:**
1. Staff opens a phone-friendly intake page (private, behind a login): snaps 1–4
   photos, types the item number and optionally a few words ("14k rope chain 24in
   20.1g").
2. Claude (vision + structured output) returns: title in their house style,
   description, category + subcategory from the decoded taxonomy, condition notes it
   can see, and a *suggested* price band with comps reasoning. **The price is a
   suggestion field, never auto-applied** — pricing authority stays human, per the
   guardrails slide.
3. The app creates a **draft product** via the Shopify Admin API (GraphQL):
   images attached, tags applied, SKU carried through, price left at the appraiser's
   number.
4. Staff reviews in Shopify admin → edits or accepts → sets Active. Same click they
   do today; everything before it is now automatic.

**Cost per listing (measured shapes, priced at Opus 4.8 $5/$25 per MTok):**
3 photos (~1.6k tokens each at high res) + prompt ≈ 6k input, ~600 output
→ **≈ $0.045 per listing.** At their observed ~60 listings/week: **≈ $12/month.**

**Pilot measurement, per the deck:** time 20 real items the current way, 20 through
the assistant, in one department. Written findings either way.

### 3.2 Catalog Normalizer — one-time backfill + webhook-driven upkeep

The rebuild proved their tags are consistent but private (HW, SH, GR, AP…) and that
naive reading misfiles Louis Vuitton under Tools. The normalizer makes the taxonomy
native:

1. **Backfill (one-time):** all ~1,565 products through the **Batch API** — title +
   existing tags in, clean canonical tags out (`dept:jewelry`, `type:ring`,
   `brand:gucci`, `metal:14k-gold`). ≈1k tokens per item → under **$10 one-time** at
   batch rates. Human spot-check of a 100-item sample before the write-back.
2. **Write-back:** tags via Admin API (products keep their existing tags; we add
   namespaced ones, so nothing staff relies on breaks).
3. **Collections:** native Shopify automated collections keyed on the new tags power
   the category pages and jewelry subfilters — no custom JS catalog.
4. **Upkeep:** a `products/create` webhook classifies each new product the same way
   (fractions of a cent each). If the Listing Assistant created it, it's already
   tagged — the webhook is the safety net for hand-entered items.

### 3.3 Wants List → Arrival Alerts (Phase 2)

Counter staff log a want ("Rolex", "PS5 Pro", "left-handed guitar") with a customer's
consent + phone number — a simple form, stored in the app's small database.
`products/create` webhook → Claude judges match quality between the new item and open
wants (structured yes/no + confidence) → queues an SMS **draft**. Sends go through
**One AI SMS** (already installed and paid for — reuse it, don't duplicate it) or
Twilio if One AI has no API path; a staff member approves the day's queue in one
sitting. Highest-intent marketing list the store can own, cost ≈ pennies/day.

### 3.4 Ask-the-Store Widget (Phase 3)

Site chat that answers hours/locations/policies from a fixed knowledge block and does
live inventory search ("do you have any PS5s?") via a **tool** that queries Shopify's
product search on their own store. Built on the SDK's tool runner: one custom
`search_inventory` tool + a system prompt with the store facts. Escalation path is
always the phone number — the widget's job is deflecting the 80% of calls that are
"are you open" and "do you have", per the deck's front-desk slide.
Estimated cost at plausible traffic (500 chats/mo, ~3k tokens each): **≈ $20–40/mo.**

### 3.5 Review Responder (Phase 3)

Weekly cron: fetch new Google reviews (Business Profile API), Claude drafts replies in
the store's voice (warm, brief, signed by the store), queue lands in an email/sheet
for a manager to approve or edit. Nothing posts unread. Cost: negligible.

---

## 4. Infrastructure

One small private web app carries everything server-side:

| Piece | Choice | Cost |
|---|---|---|
| App host (intake page, webhook receiver, cron) | Cloudflare Workers or a small VPS | $0–20/mo |
| Shopify access | **Custom app** on their store (free) — Admin API token scoped to `read_products`, `write_products` only | $0 |
| Claude API | Anthropic API key under a dedicated org for the client | usage-based (see below) |
| Database (wants list, intake sessions) | SQLite / Workers KV | ~$0 |
| Theme dev | Shopify CLI, free | $0 |

**Projected AI running cost at their volume: under $75/month all-in** — listings
(~$12), normalizer upkeep (<$5), alerts (<$5), chat widget ($20–40), reviews (<$5).
Against the $2,400/mo retainer, the COGS is ~3%. (Keep this out of the client deck;
it's your margin math. The client-facing framing stays "AI costs are included.")

**API-key hygiene:** the key lives only in the app host's secret store. Per the
guardrails slide, no customer or transaction data is ever in a prompt — merchandise
only. Anthropic's standard API data handling applies (not used for training).

### Reference shape — the Listing Assistant call

```python
import anthropic
from pydantic import BaseModel

class Listing(BaseModel):
    title: str
    description: str
    dept: str           # from the decoded taxonomy: jewelry|elec|games|music|...
    subtype: str        # ring|necklace|pendant|... ("" if n/a)
    tags: list[str]
    condition_notes: str
    suggested_price_low: float | None   # suggestion only — never auto-applied
    suggested_price_high: float | None

client = anthropic.Anthropic()

resp = client.messages.parse(
    model="claude-opus-4-8",
    max_tokens=2048,
    system=HOUSE_STYLE_AND_TAXONOMY,   # frozen prompt → cached across every listing
    messages=[{
        "role": "user",
        "content": [
            *({"type": "image",
               "source": {"type": "base64", "media_type": "image/jpeg", "data": img}}
              for img in photos),
            {"type": "text",
             "text": f"Item #{sku}. Staff notes: {notes or 'none'}. Draft the listing."},
        ],
    }],
    output_format=Listing,
)
listing = resp.parsed_output   # validated → straight into a Shopify draft product
```

The frozen system prompt (house style + the full tag taxonomy) is identical on every
call, so prompt caching cuts its cost ~90% after the first listing of each session.

---

## 5. Revised 90-day plan (same promises, Shopify-native delivery)

**Phase 0 — Access & discovery (week 0)**
Collaborator access to Shopify (themes + products). The pawn-software question from
deck slide 9: *what runs at the counter, and does the licence include API access or
Bravo's eCommerce module?* If they already pay for Bravo's sync, the Listing
Assistant's output target shifts from "typed by hand into Shopify" to "enriching what
the sync creates" — same layer, better input. Also: confirm per-store hours, collect
real featured reviews.

**Phase 1 — Launch (weeks 1–3)** *(deck: "site live on your domain")*
- Theme port: design system + templates onto their store as an unpublished theme
- Catalog Normalizer backfill + collections (this powers the category UX)
- 5 city pages as Shopify pages; LocalBusiness schema in the theme
- Quote form wired to a real inbox; analytics + call tracking
- CEO previews on the private theme URL → publish
- *Measured:* baseline traffic, calls, form fills

**Phase 2 — Throughput (weeks 4–8)** *(deck: "minutes per listing, before vs after")*
- Listing Assistant pilot in one department (the $1,500 pilot scope lives here)
- Wants-list capture at the counter; normalizer webhook live
- Staff training + written runbook
- *Measured:* minutes per listing on 20 real items, before vs after

**Phase 3 — Reach (weeks 9–12)** *(deck: "items listed per week and online revenue")*
- Listing Assistant to all departments
- Arrival alerts via One AI SMS; review responder queue
- Ask-the-Store widget
- *Measured:* listings/week and online revenue vs the week-3 baseline

---

## 6. The ask list (for the CEO, in order)

1. **Shopify collaborator access** — themes + products scopes. Unblocks everything.
2. **Pawn software answer** — vendor + whether the licence includes API/eCommerce sync.
3. **One AI SMS** — does their plan include API/automation access? (Determines
   whether arrival alerts reuse it or need Twilio.)
4. Google Business Profile manager access (review responder, later).
5. Real featured reviews + confirmed per-store hours (carried over from the site punch list).

---

## 7. Risks & honest caveats

- **Theme port ≠ copy-paste.** Liquid templating and their app embeds (Call-for-Price
  injects into product pages) need real integration work — that's the bulk of Phase 1
  effort and why launch is priced as a project, not an hour.
- **Bravo may already solve part of this.** If their licence includes the eCommerce
  sync, say so and re-scope (the deck already commits to this). The enrichment layer
  still earns its keep — the sync moves data, it doesn't *write listings*.
- **Don't promise SEO timelines.** The city pages and schema are correct plays, but
  ranking movement takes months; the deck's measurable phase gates are the honest
  yardstick.
- **One AI SMS unknown.** Never inspected its API surface; Twilio is the fallback and
  costs ~$0.01/SMS.
