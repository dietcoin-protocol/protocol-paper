# Changelog

All notable changes to the DietCoin protocol paper are documented here.

## v0.2 — 19 July 2026

Revisions following first-round design critique:

- **Purchase attestation redesigned for bundled transactions (§3.1).** UPI carries no line items; a can inside a mixed basket is invisible to the payment rail. Attestation is now tiered by evidentiary quality (Tier A: itemized quick-commerce/merchant-oracle proof, 1.0×; Tier B: bundled UPI at plausible merchant, ~0.65×; Tier C: no payment proof, ~0.3×) and applied as a mint weight, not a pass/fail gate.
- **Settlement oracle made modular by region (§3.3).** Per-can kabadiwala scanning is one mode, not the universal design. Added Mode B: aggregate settlement at the facility layer with mass-balance reconciliation — designed around Kerala's cooperative, centralized collection system (Haritha Karma Sena / Kudumbashree / MCF-RRF chain), where no field worker changes anything about their round and institutions are paid per verified tonne for data they already generate. Added Mode C: automated settlement (RVMs/smart bins), capex-gated.
- **Mint recomposed (§3.4).** Principal mint earned at verified consumption (tier-weighted); settlement becomes a per-region bonus-and-audit layer with haircuts where recovered aluminium mass fails to reconcile with claims.
- **Uniqueness anchor moved to the consumption oracle (§3.2).** Can-ID = batch-code OCR + tab-region visual fingerprint, nullified on-chain at first claim.
- **Added "Why a token at all?" (§2).** Pre-empting the index-only alternative: the token pays the sensors, a market price cannot be quietly restated, and the forward curve carries expectations no backward-looking index can.

## v0.1 — July 2026

Initial design draft: Proof of Costly Preference primitive; three-oracle can lifecycle (UPI purchase / CV consumption / per-can recycler settlement); fixed weekly emission with pro-rata split and demurrage; emission governor enforcing subsidized-never-profitable; batch-auction price discovery; data-revenue buy-and-burn anchor; minter/trader split governance; risk register.
