# DietCoin: A Consumption-Backed Macro Asset for Urban India
### Protocol Paper · Draft v0.2 · For public comment

*Author: Gopikrishnan K · dietcoin-protocol@proton.me · X: @Gopikrishn46001 · Telegram: @GKDcoin · 19 July 2026*
*Changelog v0.2: purchase attestation redesigned for bundled transactions (tiered weights, §3.1); settlement oracle made modular by region with aggregate mass-balance auditing (§3.3); mint recomposed as principal-at-consumption plus regional settlement bonus (§3.4); uniqueness anchor moved to the consumption oracle (§3.2).*

---

## Abstract

Every real-time signal of India's urban economy is either slow (quarterly GDP), coarse (surveys), or enclosed (UPI volumes, card data, ad-tech panels). We propose **DietCoin**, a decentralized protocol that converts individually verified acts of discretionary consumption into (a) a tradeable digital asset and (b) a public, real-time, city-resolved index of urban economic mood. The protocol introduces a new primitive, **Proof of Costly Preference**: where Proof of Work turns burned electricity into monetary credibility, DietCoin turns verified, unprofitable consumer spending into economic-signal credibility. A standardized soft-drink can (uniform nationwide, ~₹40, digitally purchased, recyclable) serves as the genesis "economic particle." Each can's lifecycle is verified across three layers: tiered purchase attestation, a uniqueness-anchored consumption proof, and a regionally modular settlement audit that reconciles claimed consumption against physically recovered aluminium. Coins mint against a fixed, price-governed emission schedule; price is discovered via batch auctions and anchored by a data-revenue burn. The token's price and mint-rate are designed to become public macroeconomic instruments owned by the consumers who generate them. This draft is published to invite critique and recruit a founding cohort of builders.

---

## 1. Motivation: Economic Measurement Is Centralized. Consumption Is Not.

India runs on discretionary micro-purchases: hundreds of millions of small, voluntary, digital-first transactions a day. Yet the instruments that *measure* this economy belong to institutions. NPCI publishes UPI aggregates on its own schedule; card networks and ad-tech firms sell consumer panels over consumers' heads; official statistics arrive months late. There is no live, open, verifiable read on the question that matters most for a consumption-driven economy: *how is the urban Indian consumer feeling, today, in this city?*

DePIN projects have decentralized wireless coverage (Helium) and street mapping (Hivemapper). **Nobody has decentralized economic measurement.** This paper argues India is the only country where that can be done at population scale, because the rails already exist: universal UPI and itemized quick-commerce invoicing for purchase attestation, Aadhaar-grade zero-knowledge proof-of-personhood for Sybil resistance, and a layered recycling infrastructure, from informal kabadiwala networks to Kerala's cooperative, centralized, state-organized collection system (one of the few of its kind in India), that can serve as settlement oracles *without changing how any of them already work*.

## 2. The Primitive: Proof of Costly Preference

A signal is credible in proportion to what it costs to fake. Bitcoin's insight was that monetary credibility can be manufactured from provably burned electricity. DietCoin's claim is parallel: **economic-signal credibility can be manufactured from provably costly consumption.**

When a person spends real money on a discretionary product and cryptographically verifies the act, they emit a signal that is expensive to counterfeit at scale: *"an urban consumer had surplus and mood today."* One such act is trivia. Twenty million a week, geolocated, timestamped, Sybil-resistant, and *net-costly to the actor by protocol law*: that is a macroeconomic instrument.

The constitutional invariant that makes the signal honest: **minting must always be subsidized consumption, never profitable consumption.** The protocol may refund part of a can's cost; it must never pay more than the can costs. The moment consumption becomes profitable, the signal ("someone genuinely wanted this") collapses into noise. Section 5 shows how this invariant is enforced mechanically, not by policy.

Why a Diet Coke can as the genesis particle? It is close to an ideal economic unit: standardized nationwide, priced squarely in the urban discretionary band, heavily purchased through digital channels, and it leaves a recyclable aluminium artifact whose physical mass allows the protocol to *audit its own digital claims* (§3.3). (The protocol is SKU-agnostic; see §9 on trademark.)

**Why a token at all? Why not simply publish an index?** Three reasons, each fatal to the index-only alternative. First, *incentive*: an index of verified consumption requires millions of people to perform verification acts, and nobody verifies cans for free. The token is the mechanism by which the measurement instrument pays its own sensors, and the data burn (§6) makes that funding self-sustaining rather than grant-dependent. Second, *credibility*: a published index is only as trustworthy as its publisher, and the entire motivation of this protocol (§1) is that institutional measurement is enclosed, slow, and revisable. A market-cleared price is the one statistic no publisher can quietly restate. Third, *information*: a statistical index is backward-looking by construction; it reports what consumption was. A traded asset and its forward curve (§7) aggregate dispersed, forward-looking beliefs about what consumption *will be*. The token is not a fundraising device bolted onto an index; it is what upgrades a measurement into a market, and a market into an instrument.

## 3. Architecture: Three Verification Layers

Design principle, learned the hard way: **verify against infrastructure as it actually operates, never redesign the infrastructure.** UPI does not carry line items; municipal waste workers will not scan individual cans. The architecture below meets both realities.

### 3.1 Layer 1: Purchase Attestation (tiered, not gated)

UPI transaction messages carry merchant, amount, and timestamp, **not line items**. A ₹347 kirana basket containing a can is opaque to the payment rail; item-level data lives in the merchant's billing system. Purchase attestation is therefore *tiered by evidentiary quality* and applied as a **mint weight**, not a pass/fail gate:

| Tier | Evidence | Mint weight | Channel |
|---|---|---|---|
| **A** | Itemized digital proof: quick-commerce/organized-retail invoice (consented invoice parsing, platform APIs, ONDC item-level order data) binding the SKU to the payment; or a merchant-oracle scan (below); or exact-amount UPI match | 1.0× | Blinkit/Zepto/Instamart/BigBasket, modern trade |
| **B** | Bundled UPI transaction ≥ can price at a plausible merchant category, within a plausible window of the consumption proof | ~0.65× | Kirana / general trade |
| **C** | No payment proof; consumption + settlement layers only | ~0.3× | Cash purchases, gifts |

Three properties of this design:

- **Bundling is evidence, not noise.** A mixed grocery basket is *more* consistent with organic discretionary spending than a farmer's telltale string of exact-₹40 transactions. Tier B preserves the costly signal (real money moved at retail) while honestly not claiming item visibility.
- **The quick-commerce wedge.** For the urban pilot demographic, a large share of can purchases already flow through itemized digital channels. Tier A coverage is high on day one and grows as ONDC item-level data matures.
- **Merchant oracles (opt-in).** Physical retailers can join as attestation nodes: a lightweight app scans the can at checkout alongside the existing UPI QR flow, earning the merchant a micro-fee per scan and upgrading that purchase to Tier A. Merchants become paid infrastructure; their scans also give the data product true point-of-sale geography.
- **Honest data cost.** Tier-B flows contribute to velocity and geography but not to the price-paid distribution (basket totals are not can prices). The published feed is tier-weighted, the way official statistics weight survey responses by reliability.

### 3.2 Layer 2: Consumption Proof (the uniqueness anchor)

A short liveness verification binds a human act to a specific physical can. This layer, not payment or settlement, is the protocol's **double-claim anchor**:

- Open can in hand; active-challenge gestures; anti-replay embedding search; device attestation (Play Integrity / App Attest).
- **Batch-code OCR + visual fingerprint:** the printed batch/lot code plus the micro-texture of the opened tab region (effectively a natural physical unclonable function) are hashed into a can-ID and **nullified on-chain at first claim**. One can, one claim, provably: the physical analogue of double-spend prevention, enforced at the moment of consumption rather than at surrender.

**Sybil resistance.** One verified human, one stream: zk proof-of-personhood via Anon Aadhaar / DigiLocker (nullifier-based; no biometrics stored, no identity revealed on-chain). Hard cap of two verifications per person per day with declining marginal yield. The index is **breadth-weighted**: how many people participated, not how much anyone drank. Headline statistics use medians and unique-wallet counts, never raw volume.

### 3.3 Layer 3: Settlement (modular by region; audit, not turnstile)

What settlement must prove is narrower than v0.1 assumed: *the aluminium claimed as consumed really is re-entering the recycling loop, at roughly the claimed rate.* That is a **reconciliation problem**, and reconciliation can be done in aggregate. Settlement is therefore modular, with three regional modes:

**Mode A: Per-can scan nodes.** Independent recyclers (kabadiwala networks) opt in as staked nodes with low-cost scanning kits, earning fees per can. Deployed only where the informal network *wants* the income: dense metros where per-can fees are meaningful and scanning extends existing hand-sorting. This mode is one option, not the universal design.

**Mode B: Aggregate settlement at the facility layer.** Where collection is organized and institutional, **no field worker changes anything about their round.** Kerala is the design exemplar deliberately: it is one of the few Indian states where household waste collection is cooperative, centralized, and organized end-to-end. The Haritha Karma Sena, a Kudumbashree-linked women's workforce, runs scheduled door-to-door collection under a fixed user-fee model, with material flowing through a standing institutional chain of Material Collection Facilities and Resource Recovery Facilities where it is already weighed, sorted, and logged. That existing ledger of recovered mass is precisely the audit substrate the protocol needs, and it exists nowhere else in India at comparable coverage. Kerala is therefore both the hardest test of the per-can-scan assumption and the readiest home for its replacement. The protocol integrates one level up, at the facility layer. A single staked settlement node per facility (the operator, or an aggregator such as Clean Kerala Company) attests weekly can-aluminium tonnage per ward. The protocol runs a **mass-balance reconciliation**: X cans claimed consumed in a region implies ≈ X × 13–15 g of can aluminium surfacing in that region's recovery stream, adjusted for its known recovery rate. Claims and recovered mass are matched statistically, not individually. The institution is paid **per verified tonne for data it already generates**: a new revenue line for the public system at the cost of a weighbridge reading and a signature. The protocol funds organized waste collection; it never burdens it.

**Mode C: Automated settlement.** Reverse vending machines and smart bins at high-density urban points (malls, campuses, metro stations, dark stores): instant per-can settlement, no human collector, highest integrity, capex-gated to a later phase.

### 3.4 Recomposed Mint: Principal + Regional Settlement Bonus

Because settlement is aggregate in Mode-B regions, it cannot gate an individual's coin. The mint therefore splits:

- **Principal mint**: earned at verified consumption (Layer 2), weighted by purchase tier (Layer 1).
- **Settlement bonus**: a per-region pool per epoch. Regions whose recovered aluminium mass reconciles with claimed consumption unlock the full bonus, distributed pro-rata to that region's minters (and node fees to its recyclers/facilities). Persistent gaps between claims and recovered mass trigger a **bonus haircut and a fraud flag**. A ward claiming 50,000 cans while surfacing 200 kg less aluminium than expected is exactly what consumption-fraud-at-scale looks like.

Settlement thus becomes what it always should have been: **a physical audit of the digital claims, the aluminium mass balance as the protocol's proof-of-reserves.** It simultaneously finances circularity (per-can fees in Mode A, per-tonne payments in Mode B) and writes carbon-avoidance attestations against verified recovered mass.

**Fraud posture.** We assume 5–10% residual fraud permanently. In Mode-B regions, per-can fraud below the mass-balance noise floor survives by construction; the regional bonus haircut *prices* that noise rather than pretending it away. The design goal remains economically irrelevant fraud, not zero fraud: per-identity caps, yield-below-cost (§5), tier weights, and distributional statistics make large-scale manipulation cost more than it moves the index (§7).

## 4. Issuance: Fixed Emission, Pro-Rata Split, Difficulty by Participation

A fixed pot of coins is emitted per weekly epoch and divided pro-rata among weighted verified consumptions:

```
reward per can = w · E_t / W_t
```

where `E_t` is the epoch's emission, `w` the can's tier weight, and `W_t = Σw` the epoch's total weighted verifications. Double the national participation and the per-can reward halves: Bitcoin's difficulty adjustment, translated from hashpower to consumption. Consequences:

- **Farming is self-defeating.** A farmer's own volume dilutes the per-can reward for everyone, including the farmer.
- **Supply is decoupled from any seller's incentive.** No beverage company can inflate the asset by selling more; more cans just split the same pot thinner.
- **Difficulty is itself a signal.** The public cans-per-coin ratio is a tamper-evident, weekly index of engagement intensity, decomposable by city.

**Demurrage.** Tokens idle >90 days decay ~1%/month, with decayed supply recycled into emission. This keeps circulating supply weighted toward *living participants* (the asset remains a heartbeat, not a fossil record) while structurally taxing hoard-and-squeeze manipulation. (Precedent: Gesell's depreciating currency; the Wörgl experiment, 1932.)

## 5. The Emission Governor: Enforcing "Subsidized, Never Profitable"

Let `p̄_t` be the 7-day TWAP of the token, `c` the retail can price (~₹40), and `θ` the constitutional maximum subsidy ratio (e.g., 0.5). The rupee yield per Tier-A can is `y_t = p̄_t · (E_t / W_t)`, and the invariant `y_t ≤ θ·c` is enforced by setting:

```
E_t = min( E_max ,  θ · c · W_t / p̄_t )
```

Read as a sentence: *the protocol hands out a constant real subsidy (~₹20 per fully verified can); the token price only determines how many coins it takes to deliver it.* Two consequences follow:

**(a) Appreciation accrues to holders, never marginal miners.** Price doubles, coins-per-can halves, and the miner's rupee subsidy is unchanged. No token pump can ever make over-consumption profitable; Goodhart's Law stays mechanically caged.

**(b) The supply cap is reflexive.** As `p̄ → ∞`, `E_t → 0`. Emission strangles itself as the asset succeeds, asymptotically freezing supply: halvings triggered by market success rather than a calendar.

## 6. Price: Discovered, Never Set

No formula sets the price; a formula-priced token is scrip, and the thesis dies. The market computes the price; the protocol computes only quantity (§5).

**Spot: frequent batch auctions.** Orders collect in sealed 5-minute windows and clear at a single uniform price `p* = argmax_p min(D(p), S(p))`, the price at which maximum volume trades. Everyone in a window gets the same price. Batch design (Budish et al.) eliminates spoofing, latency races, and front-running, and yields the clean discrete prints a macro index requires. A ±10% collar per batch (excess imbalance rolls forward) acts as a rate limiter, not a ceiling: a genuine move can still travel 10× in a day; a manipulative spike becomes a slow, expensive grind.

**Fundamental anchor: the data burn.** The protocol's product is a real-time, incentive-verified, city-resolved feed of urban discretionary consumption: tier-weighted, with clean price-paid data from Tier-A flows and velocity/geography from all tiers. The DAO licenses this feed (funds nowcasting Indian GDP, FMCG intelligence, researchers, planners) and auto-executes buy-and-burn with the revenue. With annual burn spend `B`, circulating supply `S`, discount rate `r`, demurrage `δ`, and revenue growth `g`:

```
p_f ≈ B / ( (r + δ − g) · S )
```

This is a Gordon-model valuation whose dividend is paid in scarcity. The honest floor hierarchy: hard floor zero; soft floor `p_f` while data revenue exists; effectively **no cost-of-production floor** (unlike Bitcoin's electricity prop). And deliberately **no ceiling**: the governor already neutralizes the only danger of a high price, and the protocol's entire ambition lives in the uncapped right tail.

## 7. Markets: From Asset to Economic Parameter

The asset becomes an *instrument* in v2:

- **Velocity futures** settling on the verified mint-rate: the forward curve is a consumer-confidence *market*, not a survey. An inverting curve before Diwali is news.
- **City spreads** (long Bengaluru velocity / short Delhi): a market price on the relative economic temperature of Indian cities.
- **Event contracts** on consumption response to the Budget, monsoon, fuel prices, an IPL final.

Manipulating the index upward now requires defeating *three* independent layers: real retail spending across Sybil-resistant identities (tier weights), unclonable per-can consumption proofs, and a regional aluminium mass balance that will expose claims unbacked by physical metal. Manipulation *is* actual economic stimulus, self-defeating on cost and glaring in both distributional and mass-balance data.

## 8. Governance: You Can Buy the Asset, Not the Republic

A protocol measuring society must not be governed by capital, or financiers will vote to corrupt the sensor. Governance is constitutionally split: **minters** (verified consumers, quadratically weighted by consumption history, per-person capped) control protocol rules, oracle standards, and data policy; **traders** receive economic exposure and zero rule-making power. Parameters explicitly owned by minter governance: θ, tier weights, demurrage rate and grace window, collar width, regional settlement-mode assignments, mass-balance tolerance bands, data-licensing policy, oracle admission.

## 9. Risks, Stated Honestly

1. **Regulatory (existential).** INR trading places the token squarely in India's VDA regime (30% tax, 1% TDS, FIU-registered exchange partners) and its data-revenue linkage invites securities analysis. Posture: foreign-foundation + Indian-Labs structure, specialist counsel from day zero, early regulator engagement. National-infrastructure ambitions cannot live in a grey zone. Mode-B settlement adds a second regulatory surface (formal agreements with municipal/state waste institutions), which is also its strength: the protocol arrives as a *funder* of public systems, not a free rider.
2. **Public health.** Subsidizing soda at population scale will be criticized, partly fairly. Defenses: the yield-below-cost invariant, hard per-person caps, settlement economics that channel protocol money into recycling institutions, and a stated path toward less contested consumption particles as the index family grows.
3. **Reflexivity.** An index that pays for the behavior it measures bends that behavior. The invariant and breadth-weighting contain it; additionally the protocol will publish its own distortion estimates, as central banks publish seasonal adjustments.
4. **Measurement noise.** Tier-B purchase proofs blur price-paid data; Mode-B settlement audits statistically, not per-can. Both are priced (tier weights, bonus haircuts) and published, not hidden. The feed's documentation will state exactly what each series does and does not verify.
5. **Trademark.** "DietCoin" trades on a mark we don't own; a generic name is reserved and the protocol is SKU-agnostic by design.
6. **Oracle fraud.** Permanent 5–10% assumed; designed to be economically irrelevant, not absent.

## 10. Status, Roadmap, and Call for Collaborators

This is a design paper, pre-code. Roadmap: (i) manual micro-pilot (one neighborhood, ~50 users, manual verification incl. a hand-run mass-balance against a local scrap buyer) to measure participation, tier mix, and fraud rates; (ii) hackathon-grade prototype: CV verification + can-ID nullifier + mint + mini batch auction; (iii) 6-month single-city pilot (Bengaluru, target 100k verified users, ~₹15–30L, grant-funded), with a parallel Mode-B design study for one Kerala municipality, ahead of a pre-seed.

**We are assembling a founding cohort:** Solidity engineers (emission governor, batch-auction clearing, EAS attestations, nullifier registry, governance), CV/ML engineers (liveness, anti-replay, visual can-fingerprinting, farming detection), mobile/backend builders (Flutter, embedded wallets, invoice parsing, Triton serving), and a mechanism-design/regulatory critic or two. Founder economics will be transparent and on-chain: disclosed allocations, 4-year vests, published constitution.

**Critique wanted most on:** tier-weight calibration and its gaming surface; the visual can-fingerprint as a uniqueness anchor; mass-balance tolerance bands and regional recovery-rate estimation; the governor's TWAP manipulation surface; collar parameterization; Account-Aggregator / ONDC attestation design; and the securities analysis of the burn.

*One line: the consumer as miner, the can as UTXO, the city as the mine, and the forward curve on a soft drink's velocity as urban India's most-watched economic number.*

---

*License: CC BY 4.0. Comments: dietcoin-protocol@proton.me · X @Gopikrishn46001 · @GKDcoin (t.me/GKDcoin) · GitHub: github.com/dietcoin-protocol/protocol-paper (Discussions open). Full pricing math (see PRICING.md), tech stack, and mechanism appendices available on request.*
