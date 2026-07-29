# Dick's Pawn Project — Complete Handoff Document

**Purpose of this file:** if Kyle ever loses access to the Claude session that
built this, hand this single document to any capable AI assistant (or read it
yourself) and the project can be recreated, continued, or delivered from
exactly this point. It contains the full state, every decision and why it was
made, all the hard-won gotchas, and where everything lives.

Written 2026-07-28 by Claude (Fable 5), at the point where the pitch is ready
to send and no client meeting has happened yet.

---

## 1. The situation in one paragraph

Kyle works for **Dick's Pawn Superstore** — a 5-location pawn chain in the
Myrtle Beach, SC area (North Myrtle Beach, Myrtle Beach, Surfside/South,
Conway, Georgetown; in business since 1987; 4.9 stars, 2,000+ Google reviews).
He was charged with creating their new website. That grew into a full pitch:
a rebuilt site (live, working proof), an 18-slide CEO pitch deck proposing an
AI-powered upgrade of their **existing Shopify store**, an implementation plan,
and a personal delivery playbook. The pitch has **not yet been sent to the
CEO**. Pricing: $1,500 pilot → $7,500 launch → $2,400/mo retainer.

## 2. Where everything lives

All under GitHub account **kylefriesmarketing** (gh CLI authed locally at
`~/tools/gh/bin/gh.exe`; portable Node at `C:\Users\kylef\tools\node`, not on
PATH — prefix it).

| What | Repo / path | Live URL |
|---|---|---|
| Rebuilt website (the proof) | `dicks-pawn-redesign` · local `C:\Users\kylef\Downloads\New folder\dicks-pawn-redesign\` | https://kylefriesmarketing.github.io/dicks-pawn-redesign/ |
| Pitch deck + all docs | `dicks-pawn-pitch` · local `C:\Users\kylef\Downloads\New folder\dicks-pawn-pitch\` | https://kylefriesmarketing.github.io/dicks-pawn-pitch/ |
| Their current site | (theirs) | https://dickspawn.com — runs on Shopify |

Deploy = plain `git push origin main`; GitHub Pages serves from main branch
root on both repos. Pages redeploys in ~1 minute.

**Key documents inside `dicks-pawn-pitch`** (each is self-explanatory; this
file is the index and the context they assume):

- `index.html` + `deck.css` — the combined 18-slide deck (see §5 for slide map)
- `DicksPawn-AI-Proposal.pdf` — 18-page PDF of the same deck
- `SHOPIFY-CLAUDE-PLAN.md` — the technical architecture: how the rebuild
  becomes their Shopify theme, the five Claude AI systems, infra, costs,
  revised 90-day plan, ask list, risks
- `DELIVERY-PLAYBOOK.md` — Kyle's 9-stage step-by-step from "send the deck"
  through meeting, pilot, launch, retainer — including a paste-ready outreach
  email, meeting talk track, and an objection/answer table
- `README.md` — how to present the deck, where every number came from

## 3. Facts about their business (all measured, none invented)

This project's credibility rests on measured claims. Every one is verifiable:

- **Catalog:** 1,565 products (1,527 in stock) pulled from their public
  Shopify feed `https://dickspawn.com/products.json` (paginate with
  `?limit=250&page=N`). Snapshot lives in the redesign repo at
  `js/products.js` (~453KB), taken 2026-07-26.
- **Category split:** jewelry ~64% of stock; the rest spans electronics,
  tools, music, games, handbags, sporting.
- **Their internal tag taxonomy, decoded** (the single most valuable
  artifact — this took real forensics and naive reading gets it WRONG):
  - `HW` = **Handbags & Wallets** (NOT hardware — LV bags misfile under
    Tools if you assume hardware)
  - `SH` = **Speakers & Headphones** (not shoes)
  - `FW` = fine watches / accessories; `FH` = fishing/outdoor;
    `OT`/`LG` = tools
  - Jewelry: `G_`/`A_` prefix = gold/silver, second letter = type:
    `R`=ring, `P`=pendant, `B`=bracelet, `E`=earring, `N`=necklace
    (so `GR` = gold ring, `AP` = silver pendant, etc.)
  - **Check jewelry codes before the two-letter department codes** or
    items misfile.
- **SKU forensics (the hand-keying evidence):** 12 distinct SKU formats,
  most ending in staff initials — TBM (609 items, jewelry), CR (306,
  music/tools), SMT (140), KC (129, games), JMS (122), EAST (53), DM (45).
  Conclusion: 6+ people hand-typing items from the pawn system into
  Shopify, each working their own department. This powers deck slide 11.
- **Site performance (measured with `curl --compressed`, same connection):**
  their homepage 863KB HTML / 94.7KB transfer / 77 scripts / 0.42s TTFB
  vs the rebuild's 7.7KB / 0.15s.
- **SEO gaps:** zero `ld+json` on their homepage; no per-city pages
  (`/pages/conway` etc. 404). **BUT their product pages already carry
  correct Product + BreadcrumbList schema** — the deck credits this
  honestly (it's a trust move and it's true).
- **Their installed Shopify apps (detected):** Call-for-Price, One AI
  (SMS/email marketing), Lucky Orange, GTM, Meta pixel. They also have an
  eBay flow.
- **Pawn software:** unknown. Bravo Store Systems owns PawnMaster and has
  an API + eCommerce sync. Which system runs at their counter, and whether
  their licence includes API access, is THE open question — deck slide 11
  presents three integration paths (A native API / B Bravo eCommerce
  module / C scheduled file export).

## 4. The architecture decision (why the plan is shaped this way)

The rebuild proved the design; it is **not** the product. Decision: **the
redesign installs as a Shopify Online Store 2.0 theme on their existing
store.** Rationale: checkout, apps, eBay flow, domain authority all
untouched; the site IS the store so sold-in-store = gone-from-site by
construction (the sync problem vanishes); publish is one click and
one-click reversible. Rejected: headless storefront (over-engineering,
re-creates the sync problem), keeping the Pages site as a second site
(duplicate content competing with their own store on Google).

Consequences:
- The 1,565 static `/p/` product pages in the redesign get **retired**
  (their real product pages already have correct schema); the product-page
  *design* ports as a restyle.
- The decoded taxonomy becomes the brain of the **Catalog Normalizer**.
- The Pages site remains as design reference and proof.

**The five Claude systems** (full detail in SHOPIFY-CLAUDE-PLAN.md) all share
one pattern: trigger → Claude API (structured outputs) → **DRAFT written into
a system staff already use** → human approves → published. Shopify draft
products ARE the human gate — no custom review UI, nothing reaches a customer
unread. The five: (1) Listing Assistant (the $1,500 pilot — photos + item
number in, draft product out, ≈$0.045/listing), (2) Catalog Normalizer
(Batch API backfill of all items <$10 one-time, then webhook upkeep),
(3) Wants List → arrival alert SMS drafts, (4) Ask-the-Store chat widget
(inventory search tool), (5) Review responder queue.

**Hard rules baked into everything:** the AI never sets a price (suggestion
field only, appraiser's number is what goes live); merchandise only — no loan
data, no customer identity ever enters a prompt; nothing auto-publishes.

## 5. The deck — slide map (18 slides, index.html)

Deep-link with `#sN`. Keyboard: arrows/space; `P` prints (landscape +
background graphics ON for PDF export).

1. Cover — "Where AI actually pays for itself" (has the Shopify line)
2. Proof — the live rebuilt site
3. Homework — catalogue bars (1,565 items, category split)
4. Measured gaps table (perf, schema, city pages — and what they do right)
5. Theme connection — 4-step install flow + 3 green guarantees + rejected
   alternatives footnote
6. Sync-vanishes before/after (dark slide)
7. The AI pattern — speed-not-cleverness thesis + 5-step flow + drafts card
8. Listing Assistant (price-tag visuals: 5¢/listing, 2 weeks)
9. Catalog Normalizer
10. Systems 3–5 (wants list, chat widget, review responder)
11. Counter integration — pipe diagram + paths A/B/C + SKU evidence bar
12. ROI calculator — LIVE inputs (items/wk, min/item now, min with pipeline,
    $/hr, avg price, capacity %) → outputs (hours, $, annualized, extra
    items, GMV). Input ids `c-*`, output ids `o-*`.
13. Guardrails — 5 never-does + "one thing I'll always do"
14. 90-day plan (3 phases with measured gates)
15. Four asks (collaborator access flagged urgent)
16. Why me
17. Pricing — $1,500 pilot ("Start here") / $7,500 launch / $2,400/mo;
    anchor box ends "all AI usage costs are included"
18. Close — stats: $1,500 / 2 weeks / 0 systems replaced or migrated

**Pre-send punch list (still open):** put the CEO's name on the cover, put
Kyle's phone number on slide 18.

## 6. Money (Kyle's private numbers — never client-facing)

- Projected AI running cost at their volume: **under $75/month all-in**
  (~3% of the $2,400 retainer). Client-facing wording is always
  "AI usage costs are included" — never state the margin.
- Cost per listing ≈ $0.045 (3 photos ≈ 6k input tokens + ~600 output at
  Opus-tier pricing). Catalog backfill < $10 one-time via Batch API (50%
  off). These two numbers CAN be said to the client — they make the tech
  sound as cheap as it is.
- Model policy: top-tier Claude model for anything customer-facing; Batch
  API for the backfill; frozen system prompt → prompt caching ≈90% off
  input after the first call.
- Invoicing: pilot $750 on signing + $750 with findings; launch $6,000
  after the $1,500 credit, half up front; retainer monthly, 30-day cancel.

## 7. The redesign repo — what's in it and how it works

The rebuilt site is a static build with a generation pipeline:

- `index/shop/sell/services/about/locations.html` — the six root pages.
  SVG icons inlined at build time, OG tags → `assets/og-card.jpg`
  (1200×630, built free with System.Drawing from real product shots),
  PawnShop JSON-LD, `<main id="main">` landmarks for skip links.
- `js/products.js` — the 453KB catalog snapshot. `js/details.json` —
  galleries/descriptions for product pages.
- `js/shop.js` — search/filter/jewelry-subpills/quick-view.
  `renderLatest(elId, n, capPerCat)` caps 3 items per department (honesty
  fix — see gotchas). `PAGE_URL='p/'`.
- `js/icons.js` — hand-drawn 24×24 stroke icon set, used at build time by
  `tools-icons.mjs` AND at runtime.
- `js/nav.js` — mobile nav panel; **path prefix is derived from the page's
  own nav href** (so generated pages at `/p/` depth resolve correctly).
- `js/enhance.js` — hero/reveal animations, all fail-safe (see gotchas).
- `gen-products.mjs` — the taxonomy-correct categorizer (jewelry codes
  before department codes; memorabilia regex deliberately excludes
  "signature" because of Signature Series guitar pedals).
- `gen-pages.mjs` — generates the 1,565 `/p/` pages + 5 `/locations/`
  pages + sitemap.
- `serve.mjs` — local dev server (`PORT` env; serves json/xml/txt MIME).

To refresh the catalog: re-pull products.json pages from their feed →
rerun `gen-products.mjs` → `gen-pages.mjs` → push. (Under the theme plan
this pipeline retires; keep it until the theme ships.)

## 8. Gotchas — the expensive lessons (do not relearn these)

**Environment (Windows, this machine):**
- Headless Chrome `--print-to-pdf`/`--screenshot` **fails silently with
  relative paths** because the workspace path contains a space
  ("New folder"). Always write to an absolute scratchpad path, then copy.
- `node -e` writing to `/tmp` fails (C:\tmp ENOENT under node fs even
  though git-bash `/tmp` works). Use absolute paths.
- PowerShell 5.1: no `&&`; UTF-8 punctuation in inline scripts becomes
  mojibake and parser errors — keep inline PS pure-ASCII, use
  `[char]0xNNNN` for special chars. Enum syntax:
  `[Namespace.Type]::Member`, not `::Type.Member`.
- Long git commit messages inline can die in the LF/CRLF warning flood —
  use `git commit -F <file>` or a heredoc via `-F -`.
- The Claude Code Browser pane runs pages with `document.hidden = true`,
  so **CSS transitions/animations never advance** — working code looks
  broken. Twice this caused false bug reports against the site. Proof
  method: disable the transition and read the resting computed value, or
  DOM-check. Never trust an opacity mid-animation in that pane.
- PDF page-count check without poppler: `grep`-style search the PDF bytes
  for `/Count N` markers (the root Pages node has the total).

**Site logic:**
- The locations page bug (user-reported: all store buttons under North):
  splitting page HTML by street-address `indexOf` matched the head's
  JSON-LD first. Fix: split body on `/(?=<div class="loc-card)/` —
  and note `loc-card map-card` exists, so exact-string split misses it.
- JS `String.replace` with `$` in the replacement string: `$` is an escape
  — use a function replacement for anything containing prices.
- "Fresh this week" was dishonest (newest 8 items were all jewelry, 9–31
  days old). Fix: heading became "Latest arrivals on the floor" and
  renderLatest caps 3/department. Nothing is backdated — keep it that way.
- Hero/reveal animations are fail-safe by design: hero cards are visible
  in CSS by default and only opt INTO animation via an `is-anim` class
  stripped after 1400ms; scroll reveals have a 2.5s watchdog that
  force-reveals if the IntersectionObserver never fires. Do not "clean
  up" this redundancy — content must never be gated on an effect firing.
- Deck CSS: `.bar-fill` spans need `display:block` or bars render empty;
  `.slide.dark h3` outspecifies `.ba h3` (re-assert card heading colors
  with higher specificity on dark slides); calculator labels need
  `.dark .field label{color:#fff}`.

**Verification recipe for the deck:** `node serve.mjs` in the pitch repo →
headless Chrome `--screenshot` per slide (`?print` query or `#sN`) to an
absolute scratchpad path → Read the PNGs. Live-deploy check: poll the Pages
URL with a cache-buster and count `class="slide` occurrences.

## 9. Current state & what happens next

**Done and live (as of 2026-07-28):**
- Rebuilt site: full catalog, product pages, location pages, mobile nav,
  OG share card, SVG icon system, accessibility landmarks — deployed.
- Combined 18-slide deck + 18-page PDF — deployed, spot-verified.
- SHOPIFY-CLAUDE-PLAN.md, DELIVERY-PLAYBOOK.md, README — in the repo.

**Not done (the road ahead, in order — DELIVERY-PLAYBOOK.md is the detailed
version of this list):**
1. Personalize deck cover + phone number; decide repo-privacy question.
2. Send the outreach email (draft is in the playbook, Stage 1).
3. Run the CEO meeting (talk track + objection table in playbook Stage 2).
4. Pilot agreement + first invoice (Stage 3).
5. Access week: Shopify collaborator (Themes + Products scopes), custom
   app with `read_products`/`write_products` token, Anthropic org with
   spend cap, prove one draft-product write end-to-end (Stage 4).
6. Build the Listing Assistant pilot; measure 20-vs-20; findings doc
   (Stage 5). Then launch close → theme port (start from Dawn, Shopify
   CLI, unpublished preview, watch the Call-for-Price app embed) →
   normalizer backfill → phases 2–3 → retainer (Stages 6–9).

**Open questions that shape the work:**
- What pawn software runs at the counter, and does the licence include
  API access or Bravo's eCommerce module? (Decides integration path
  A/B/C; if they already pay for Bravo sync, the Listing Assistant
  enriches sync output instead of replacing hand-keying — same layer,
  better input. The deck already commits to telling them this honestly.)
- Does One AI SMS's plan include API/automation access? (Reuse it for
  arrival alerts; Twilio at ~$0.01/SMS is the fallback.)

## 10. Instructions to a future AI assistant taking this over

You are picking up mid-project for Kyle (GitHub kylefriesmarketing).
Read, in order: this file → README.md → SHOPIFY-CLAUDE-PLAN.md →
DELIVERY-PLAYBOOK.md. Then:

- **Never invent numbers.** Every business claim in this project is
  measured; the sources table in README.md maps claim → source. If you
  add a claim, measure it first or mark it clearly as an assumption.
- **Respect the guardrails as product decisions, not suggestions:** drafts
  only, no auto-publish, no AI pricing, merchandise-only in prompts. They
  are load-bearing in the pitch — the client is being sold these promises.
- **Keep the private/public boundary:** the <$75/mo AI cost figure and the
  ~3% margin never appear in client-facing material.
- Kyle's working style: batch work, paste-ready outputs, forward momentum,
  verify-then-ship, token-lean (don't read binaries; DOM-check over
  screenshots where possible). Push to a finished verified result without
  check-ins on reversible work; confirm before irreversible or
  outward-facing actions (anything sent to the client counts).
- Never create accounts, enter credentials, or send anything to the CEO
  on Kyle's behalf. Draft it; Kyle sends.
- The state of truth is the two repos. If this file and the code disagree,
  trust the code and update this file.

*End of handoff. — Claude (Fable 5), 2026-07-28*
