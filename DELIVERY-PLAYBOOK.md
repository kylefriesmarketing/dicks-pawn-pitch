# Delivery Playbook — Dick's Pawn (Kyle's copy, not for the client)

Step-by-step from "deck is ready" to "retainer running". The deck makes the
promises; `SHOPIFY-CLAUDE-PLAN.md` is the architecture; this file is the order
you actually do things in, with the commands, the scripts to say out loud, and
the points where money changes hands.

The one rule underneath all of it: **never promise a date you haven't hit once
in private first.** Every phase below has you do the thing quietly before you
demo it.

---

## Stage 0 — Before you send anything (1 evening)

1. **Personalize the deck.** Open `index.html`, put the CEO's name on the cover
   and your phone number on slide 18. Commit + push (Pages redeploys in ~1 min).
2. **Do one full practice run.** Present the deck out loud to nobody, arrow keys,
   start to finish, twice. You should be able to say slide 11 (the counter
   integration) and slide 13 (guardrails) without reading them — those are the
   two slides where she'll interrupt with questions.
3. **Decide the repo-privacy question now.** The deck carries your pricing at a
   public URL. Either you're fine with that (it's only reachable if you have the
   link) or you send the PDF instead. Don't discover this concern mid-meeting.
4. **Know your own numbers cold:**
   - Pilot $1,500 · Launch $7,500 · Retainer $2,400/mo · Ad-hoc $125/hr
   - Your AI cost at their volume is under $75/mo. **Never say this number to
     the client.** The client-facing words are "all AI usage costs are included."
   - Cost per listing ≈ $0.045. Catalog backfill < $10 one-time. These two you
     *can* say — they make the tech sound as cheap as it actually is.

## Stage 1 — Send it and get the meeting

Email or text, short. Paste-ready:

> Hi [CEO] — I finished the homework I mentioned. I catalogued all 1,565 items
> in the online store, measured the current site's performance, and built a
> working preview of what a rebuilt one looks like with your real inventory:
> https://kylefriesmarketing.github.io/dicks-pawn-redesign/
>
> Then I put together a short deck on what I found and what I'd propose —
> including how AI can take most of the typing out of getting items online,
> without touching your pawn system or your Shopify checkout:
> https://kylefriesmarketing.github.io/dicks-pawn-pitch/
>
> 20 minutes this week to walk you through it? The first step I'm proposing is
> small on purpose — two weeks, one department, $1,500, credited toward the
> full project if you continue.

Notes on the ask:
- You want a **live walkthrough**, not "I'll look when I get a chance." The deck
  survives being read alone, but the calculator (slide 12) only lands when she
  types her own numbers into it.
- If she forwards it to someone technical, good — slide 11 and the plan doc were
  written to survive that reader.

## Stage 2 — Run the meeting (20–30 min)

Order of operations:

1. **Open on the live rebuild, not the deck.** Two minutes clicking around the
   preview site on your phone. "This is your actual inventory, rebuilt. Loads in
   a tenth of the time." Then switch to the deck.
2. Slides 1–4 fast (2 min). The homework slides earn trust; don't linger.
3. **Slide 5 is the fear-killer.** The message, verbatim: "Nothing migrates.
   Your store, your checkout, your apps stay. The new design installs as a
   theme you preview privately, publish with one click, and can revert with one
   click." Say "one click back" twice.
4. **Slide 12: hand her the keyboard.** Ask: how many items a week do you list,
   and how long does one take? Type *her* numbers. This is the moment the deck
   stops being your claims and becomes her math.
5. **Slide 13 before she asks.** Preempt: "Here's what the AI never does —
   never publishes unread, never sets a price, never touches loans or customer
   data." Pawn people are compliance people; this slide is why she can say yes.
6. **Close on slide 17, pilot only.** Do not sell the $7,500 in this meeting.
   "The only decision today is the $1,500 pilot. Two weeks, one department,
   written findings either way, credited in full if we continue."

Objections you should expect, and the answers:

| She says | You say |
|---|---|
| "We already have a website." | "You do, and your product pages are actually set up right — I checked. This keeps all of that. It's the same store with a faster front and the typing automated." |
| "Is AI going to write wrong prices?" | "It can't set a price. It suggests a range in a field a human reads; the appraiser's number is what goes live. That's a hard rule, not a setting." |
| "What about our pawn software?" | Slide 11. Then ask HER question back: "What runs at the counter, and does your licence include API access? That answer picks the path — I've scoped all three." |
| "We have a person who does the website." | "Perfect — this makes that person faster, not redundant. The pilot measures their 12 minutes per item dropping to 4. They approve everything; the AI does the typing." |
| "How do I know this isn't a subscription trap?" | "Everything installed is on your accounts — your Shopify, your theme, your data. Cancel any time with 30 days' notice and it all keeps working. You're renting my time, not my software." |
| "$2,400/month is a lot." | "It's optional and it's month-to-month after the first quarter. A part-time e-commerce hire is $56–75k with payroll burden. And the retainer question isn't today's decision — the pilot is." |

**Before you leave the room, get the pilot yes AND the two answers:** which
pawn software runs at the counter, and who can grant Shopify collaborator
access. Walking out with the yes but not the access contact costs you a week.

## Stage 3 — Paper and money (same day as the yes)

1. **One-page agreement.** Keep it to: scope (pilot as described on slide 17),
   price ($1,500, half up front, half on delivery of findings), duration
   (2 weeks from access granted — not from signature; access delays are their
   delay, not yours), what they get (working pipeline in one department +
   written findings), the credit clause ($1,500 credited to launch if they
   proceed within 60 days), and the data rule (merchandise only, no customer or
   loan data ever processed). Plain English beats legalese at this size.
2. **Invoice #1** — $750 on signing. (Invoice #2, $750, goes out with the
   findings document.) Use whatever you already invoice with; if nothing, Wave
   or PayPal invoicing is fine at this size.
3. **LLC note:** you have The Lunch Desk LLC. Doing this work under an LLC
   umbrella (that one, or a new one if you want the brand separation) is worth
   it for the liability line alone. Not a blocker for the pilot.

## Stage 4 — Access week (their week 0, your setup week)

**From them:**
1. Shopify collaborator access: they go to `Settings → Users → Collaborators`
   in their Shopify admin (or you send a collaborator request from a [Shopify
   Partners](https://partners.shopify.com) account — free to create, and you
   want one anyway; it also gives you free dev stores to practice on). Scopes
   you need: **Themes**, **Products**. Nothing else — asking for minimal scopes
   is itself a trust move; say so.
2. The pawn-software answer (vendor + licence details). Don't block on it; the
   pilot works with hand-keyed input regardless. It decides Phase 2 shape.
3. Which department pilots. Steer to **jewelry** — highest volume (64% of
   stock), most formulaic listings, best before/after numbers.

**Your side, same week:**
1. **Anthropic API**: console.anthropic.com → create a **separate organization**
   for client work (clean books, clean data boundary). Add a payment method,
   set a **monthly spend limit** ($50 — a runaway loop can't hurt you), create
   an API key. The key lives only in the app host's secret store, never in the
   repo, never on your laptop in plaintext.
2. **App host**: Cloudflare Workers (free tier covers this easily). One Worker,
   one KV namespace. Alternative: any $5 VPS — but Workers means no server to
   patch for the life of the retainer.
3. **Shopify custom app** (this is distinct from your collaborator login — it's
   the API identity the pipeline writes with): their admin →
   `Settings → Apps and sales channels → Develop apps → Create app` → Admin API
   scopes `read_products`, `write_products` → install → copy the Admin API
   token into the Worker's secrets. If "Develop apps" is disabled, the store
   owner enables it once.
4. **Verify the loop end-to-end with a dummy item** before building any UI:
   one curl to the Admin GraphQL API creating a product with `status: DRAFT`,
   confirm it appears in their admin as a draft, delete it. The whole pilot
   rests on that one write working.

## Stage 5 — The pilot build (their weeks 1–2)

Build order — each step produces something testable:

1. **The system prompt** (biggest quality lever, ~a day of iteration):
   house style + the full decoded tag taxonomy + output rules. Feed it 10 of
   their real existing listings as style examples. Test against photos of items
   already in their store and compare Claude's output to what staff wrote —
   that's your quality benchmark, free. Freeze it (frozen prompt = prompt
   caching = ~90% off input costs).
2. **The API call**: `client.messages.parse()` with the Pydantic `Listing`
   model — the exact reference shape is in `SHOPIFY-CLAUDE-PLAN.md` §4. Model:
   `claude-opus-4-8`. Suggested price is a *suggestion field*; nothing in your
   code ever writes it to the price.
3. **The intake page**: one mobile page behind basic auth — photo input
   (`<input type="file" accept="image/*" capture="environment">` opens the
   camera directly), item number, optional notes, submit, then a "draft
   created — review in Shopify admin" confirmation with a deep link to the
   draft. Test it standing up, one-handed, in bad light. That's how it gets
   used at a counter.
4. **The Shopify write**: create the draft product via Admin GraphQL
   (`productCreate` with `status: DRAFT`, then attach images, tags, SKU).
   Draft status is the human gate — belt-and-suspenders assert it in code.
5. **Dry-run with yourself**: 10 items photographed off their public site, full
   loop, before anyone at the store touches it. Fix everything that annoys you.
6. **On-site day**: train the pilot-department staff in person, 30 minutes.
   Let *them* run items 11 onward. Every complaint is free product research —
   write them all down.
7. **Measure exactly what the deck promised**: 20 real items the current way
   (stopwatch, their person, their process), 20 through the assistant. Same
   item mix. Record minutes per item, corrections staff made to AI output, and
   anything the AI got embarrassingly wrong (there will be some — they go IN
   the findings doc; honesty here is what sells the retainer).

**Findings document (the pilot deliverable):** 2–3 pages. Timed results table,
what staff said, what the AI got wrong and how often, cost per listing actually
observed, and a recommendation. Send with invoice #2. If the numbers are bad,
say so and shake hands — the deck promised that, and a clean no protects your
reputation in a town where pawn owners talk to each other.

## Stage 6 — Present findings → close the launch

Short meeting: findings, then "Phase 1 is the theme going live on your store
plus the catalog reorganization — $7,500 minus your $1,500 credit = $6,000,
half up front." Same close discipline: the only decision is Phase 1; the
retainer conversation happens at the end of the 90 days when the report card
exists.

## Stage 7 — Theme port (their weeks 1–3 of the 90-day plan)

The bulk of real engineering. Order:

1. `npm i -g @shopify/cli` → `shopify theme list --store their-store` to
   confirm access works.
2. **Start from Dawn** (Shopify's reference theme): `shopify theme init`.
   Port your design system ONTO Dawn's skeleton rather than building Liquid
   from scratch — Dawn already handles cart, variants, sections, a11y, and
   app blocks correctly. Your `style.css` tokens, SVG icon set, and layouts
   restyle it. Resist the urge to port your HTML wholesale; port the *look*.
3. Port order: theme tokens/typography → header/nav/footer → homepage sections
   (hero with live product references, action strip, category tiles) → product
   page restyle → collection page + jewelry subfilters (native storefront
   filtering on the normalized tags) → sell/services/about/locations templates
   → 5 city pages (`page.location` template).
4. **App embeds are the landmine** (plan §7): Call-for-Price injects into
   product pages. Test it on the dev theme early, not at the end. Same for
   their One AI popups and analytics tags.
5. Workflow: `shopify theme dev` for live local preview →
   `shopify theme push --unpublished` → **share the preview URL with the CEO**
   (this is the "you preview privately" promise from slide 5 — deliver it as
   an actual link she can open on her phone).
6. Meanwhile run the **Catalog Normalizer backfill** (plan §3.2): Batch API,
   all 1,565 items, human spot-check of 100 before any write-back, then add
   namespaced tags (`dp:dept:jewelry` style — never touch their existing
   tags) and build the automated collections on them.
7. Baseline metrics BEFORE publishing the theme: traffic, calls, form fills —
   whatever exists. The 90-day report card needs a before.
8. Publish when she approves. The old theme stays in the library — that's the
   one-click rollback, and you tell her where it is.

## Stage 8 — Phases 2–3 (weeks 4–12)

Per the plan doc, in order: Listing Assistant to the pilot department for real →
wants-list capture + normalizer webhook → assistant to all departments →
arrival alerts (through One AI SMS if their plan has API access, Twilio at
~$0.01/SMS if not) → review responder → Ask-the-Store widget. Each one ships
the same way the pilot did: build → dry-run on yourself → train → measure.

Measure at every phase gate the deck named: minutes per listing (Phase 2),
listings/week + online revenue vs week-3 baseline (Phase 3).

## Stage 9 — The retainer (day 90+)

1. Write the 90-day report against the baselines. Her inputs from the slide-12
   calculator, now with real observed numbers next to them.
2. If the numbers are good, the $2,400/mo conversation closes itself. Bill
   monthly, 30-day cancellation, month-to-month after the first quarter — all
   as slide 17 promised.
3. Monthly retainer rhythm: keep pipelines running, one improvement shipped per
   month, a one-page numbers email against baseline (send it even when nobody
   asks — the unrequested report is what makes renewal automatic), and the
   approval queues (reviews, alerts) never left stagnant.
4. Watch your Anthropic console monthly. Costs should sit under $75/mo; if
   something spikes, you set a spend cap in Stage 4, so it can't run away —
   find the loop before it finds your margin.

## Rules that don't bend (your own guardrails)

- **Never enter credentials for them, never create accounts on their behalf.**
  They create the collaborator access, the custom app, the API tokens on their
  side; you receive access. This is both proper security and clean liability.
- **Draft status is sacred.** No code path ever creates an `ACTIVE` product or
  publishes anything customer-facing without a human click. If a client ever
  asks you to auto-publish "to save time", the answer is the guardrails slide.
- **Merchandise only.** No loan data, no customer identity ever enters a
  prompt. If a future feature seems to need it, redesign the feature.
- **Say what broke.** In findings docs and monthly reports, the misses go in
  next to the wins. It's the cheapest trust you'll ever buy.
- Deck promises are the contract. When in doubt about scope, reread slides
  13–17 — deliver exactly that, in that order, and nothing is ever awkward.
