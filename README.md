FeeOne — Fee & Pricing Engine for Transaction Banking

A single-file, zero-dependency web app that captures the complete commercial logic of a world-class corporate transaction-banking fee engine — live, explainable, and verified.

Every pricing rule a global transaction bank uses — earnings credit (ECR), tiered and banded pricing, bundles, clubbing, channel differentials, trade-finance commissions, relationship deals — implemented as interactive calculators with a Stripe-receipt-style resolution trace that shows exactly how every number is computed.


🔢 6 pricing axes · 21 live business cases · 4 ECR models · 39 + 31 automated tests passed




✨ Live demo

Deploy your own in under a minute (see Deployment) or just open index.html in any browser — the app is fully self-contained and works offline.

DesktopMobileSticky blurred nav, card grid, live tracesScrollable segmented nav, single-column, 16px inputs (no iOS zoom)


🧭 What this is

FeeOne is the interactive companion to a consolidated research base on how the leading transaction banks (Citi, J.P. Morgan, HSBC, Standard Chartered, Goldman Sachs TxB, Morgan Stanley) and pricing platforms (Oracle ORMB, Zafin, Infosys Finacle, AI-native billing) price corporate cash management, payments, receivables, liquidity and trade finance.

It is built for:


Product managers designing fee engines, billing platforms or account-analysis systems
Relationship managers who need to explain or simulate a client's bill
Engineers who need the exact calculation rules before writing a line of platform code
Treasurers who want to understand how their bank's charges and ECR actually work



📱 Sections at a glance

TabWhat it containsOverviewThe six pricing axes, the 5-layer pricing-precedence ladder, benefit-stacking modes, and what each major bank publicly signalsECRFour live models: reserve-adjusted (Act/365), analysed account (simple ÷12), balance-tiered (marginal bands), and hybrid ECR + credit interest (HSBC pattern: offset → surplus payout → above-threshold interest)Bundles & TiersTiered (retroactive) vs banded (marginal) side-by-side, package pricing with allowances + overage, volume clubbing across entities, cross-product discounts with ECR-uplift valuePayments & LiquidityMonthly payments statement with channel differentials and correspondent pass-through (plus an engine-surfaced optimisation), notional pooling economics, ZBA sweeps, liquidity-structure fees, receivables bundle, virtual accounts, FX marginTrade & SCFImport LC with part-period round-up (per quarter), documentary credit (per month), guarantee with mid-term amendment re-rating, trade loan (Act/360), supply-chain financeCase LibraryAll 21 worked business cases, computed live by the engine at page load — tap any row to jump to its calculator with editable inputsRevenueThe five revenue buckets (explicit fee / balance / market / financing / relationship), a live relationship roll-up, economic-vs-billed, and the 3–8% leakage thesisReferenceFee taxonomy (8 behavioural classes), charging-model formulas, the full parameter catalogue, the 7-layer reference architecture, delivery roadmap and MVP definition


🧮 The business rules captured

Every rule below is implemented as a pure function and verified against the master document:

RuleImplementationTiered (retroactive)Reached-band rate applies to all units → 600 wires @ $20/$15/$10 = $6,000Banded (marginal)Each band's units at that band's rate → same 600 wires = $9,000Package + overagebase + Σ max(0, used − allowance) × overage → $7,360ClubbingAggregate entity volumes through the same bands → $7,700 → $6,500 (saves $1,200)Reserve-adjusted ECRcollected × (1 − reserve) × ECR × days/365 → credit $16,643.84, net fee $0, unused expires (no-carry)Simple-monthly ECRbalance × rate ÷ 12 → $40,000 allowance vs $31,700 charges → $0 billed, $8,300 expiresBalance-tiered ECRMarginal bands (1.75 / 2.25 / 2.75%) on $25m → $48,287.67Hybrid ECRoffset = min(allowance, charges) + surplus payout + (balance − threshold) × interest ÷ 12 → client value $23,333.33Channel & pass-throughManual > electronic; repairs surcharged; correspondent itemised → $5,810, optimisation −$310Notional poolingClient benefit offset × spread × 30/365 = $66,575.34; bank fee $1,979.45; client net $64,595.89LC part-period round-upperiods = ceiling(tenor ÷ charging period) → 7-month LC at 0.20%/quarter = 3 periods → $14,500 lifecycleGuarantee amendmentBase $15,000 + increase re-rated for remaining months $750 + event fee → $15,900Trade loan / SCFAct/360 financing: $16,250 + $1,250 arrangement = $17,500; SCF 45-day discount = $8,750Relationship roll-upSix revenue lines summing live to $43,086.93/month

Plus the structural rules: the 5-layer precedence stack (deal → bundle → package → tariff → default), 4 benefit-stacking modes (additive / best-of / selected / exclusive), and the principle that price is data, never code.


🚀 Deployment

Netlify (drag & drop — recommended)


Go to app.netlify.com/drop
Drag FeeOne_App.zip (or a folder containing index.html) onto the page
Done — your site is live on a *.netlify.app URL. No build step, no configuration.


GitHub Pages


Push this repo to GitHub
Settings → Pages → Source: Deploy from a branch → select main / root
Your app is live at https://<username>.github.io/<repo>/


Anywhere else

It's one static HTML file. Vercel, S3, Cloudflare Pages, an intranet share, or double-clicking the file all work.


📂 Project structure

.
├── index.html      # The entire app — markup, design system, engine, UI (zero dependencies)
└── README.md       # This file

That's deliberate. One file means: nothing to install, nothing to build, nothing to break, and it runs offline.

Inside index.html:

<style>      → design tokens & components (≈300 lines)
<script #1>  → FE.* calculation engine — pure functions, no DOM (≈130 lines)
<script #2>  → UI layer — tabs, section builders, live wiring (≈700 lines)


✅ Verification (no hallucinated numbers)

The calculation engine was tested in isolation, before any UI existed:


39 engine assertions — every business case checked against the figures in the consolidated master document (e.g. hybrid client value 23,333.33, pooling net 64,595.89, LC lifecycle 14,500)
31 headless DOM tests — the rendered page was loaded in a DOM environment, every tab built, and every displayed figure asserted
Visual review at 1280px desktop and 390px mobile viewports


To re-run the spirit of these checks yourself, open the browser console on the Case Library tab — all 21 results are produced by FE.* calls at load, not hard-coded strings.


🎨 Design notes

Inspired by Apple (system font stack, translucent blurred header, segmented controls, generous whitespace, restraint) and Stripe (a single indigo accent, hairline borders, tabular numerals, receipt-style line items).

The signature element is the resolution trace: every calculator renders a live, line-by-line statement — each line carries its formula in grey, credits show in green, and the bound figure is the big number. This mirrors what a fee engine fundamentally is: explainable, itemised pricing (“why this fee applied” on every charge).

Quality floor: responsive to small phones, visible keyboard focus rings, prefers-reduced-motion respected, 16px inputs to prevent iOS auto-zoom, no external fonts or CDNs.


🗺️ Engine concepts (for builders)

If you're implementing a real platform from this base, the load-bearing ideas are:


Six axes — scope, event, balance, time, channel, relationship. Every price is a point in this space, resolved from configuration.
Deterministic precedence — most specific wins; exactly one resolved price and one explanation per event.
ECR is first-class — classic, tiered and hybrid modes; reserve factor, day-count, carry rule and surplus payout are all parameters.
Economic ≠ billed — a $0 invoice under ECR is not $0 revenue; maintain both ledgers.
Part-period rules are leakage — round-up on tenor commissions and minimum fees must be native, not manual.
Statements are standards — camt.086 / TWIST BSB keyed to AFP Global Service Codes.



⚠️ Disclaimer

All rates and amounts are illustrative, constructed to demonstrate engine mechanics. They are not any institution's current negotiated tariff. Bank and vendor references reflect public-record materials consolidated in the companion master document and Excel workbook.
