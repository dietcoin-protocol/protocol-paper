# The DietCoin Pricing Algorithm
### How the price of a DietCoin is discovered, anchored, and protected — without ever being *set*

---

## 0. The Core Principle (Read This First)

**No formula sets the price of DietCoin. Ever.**

If an equation dictated the price, DietCoin would be scrip — company points with extra steps — and the entire thesis (the market discovering the value of verified consumption) would die. Bitcoin has no pricing equation either; it has a *quantity* equation, and price emerges from the market.

DietCoin does the same, with one elegant inversion:

> **Bitcoin fixes the quantity schedule and lets price float.**
> **DietCoin lets price float and algorithmically adjusts the quantity minted per can.**

So "the pricing algorithm" is really four pieces of math *around* the price:

1. **The clearing rule** — how the market computes the price (price discovery)
2. **The emission governor** — how the protocol reacts to the price (quantity control)
3. **The fundamental anchor** — what the price gravitates toward (valuation)
4. **The collars** — how the price is protected from manipulation (stability)

Also note: a *newly minted* coin has no special price. The protocol never sells coins — it distributes them for verified acts, and the market prices every coin identically, fresh or old.

---

## 1. Price Discovery: The Batch Auction Clearing Rule

### The math

Every 5 minutes, all buy and sell orders are collected. From them, build:
- **D(p)** = total DTC demanded at price ≤ p (the demand curve)
- **S(p)** = total DTC offered at price ≥ p (the supply curve)

The clearing price is:

```
p* = argmax over p of:  min( D(p), S(p) )
```

Every trade in that window executes at the single price **p\***. Ties break toward the midpoint.

### The intuition

Imagine everyone who wants to trade in the next 5 minutes writes their order on a slip and drops it in a box. When the timer rings, we ask one question:

> **"At what single price would the most slips get matched?"**

Think of it like a school canteen selling samosas at one fixed price for the whole lunch break. Set the price too high — lots of samosas, few buyers, little gets sold. Too low — a mob of buyers, but sellers hold back. Somewhere in the middle is the price where the *maximum number of samosas actually change hands*. That sweet spot is p\*.

Why one price per window instead of a continuous ticker?
- **Fairness:** everyone in the window gets the same price — no advantage for faster internet or trading bots.
- **Manipulation resistance:** classic tricks (spoofing, momentum ignition, front-running) all rely on continuous, order-by-order execution. A sealed batch makes them useless.
- **Clean data:** each 5-minute print is one crisp, honest data point — exactly what a macroeconomic index needs.

---

## 2. The Emission Governor: How Minting Responds to Price

### The math

Definitions:

| Symbol | Meaning | Example value |
|---|---|---|
| p̄ₜ | Trailing average price (7-day TWAP) | ₹25 |
| Vₜ | Verified can-lifecycles this epoch | 500,000 |
| c | Retail price of a can | ₹40 |
| θ | Max subsidy ratio (constitutional) | 0.5 |
| Eₜ | Coins emitted this epoch | *computed* |

Yield per can in rupees: `yₜ = p̄ₜ · (Eₜ / Vₜ)` — must never exceed θ·c.

Therefore the governor sets:

```
Eₜ = min( E_max ,  θ · c · Vₜ / p̄ₜ )
```

### The intuition

The constitution says: **drinking a Diet Coke must always cost you money, net-net.** Minting can *discount* your can (recover up to half its price) but never *pay* you to drink. Otherwise, people drink for profit, and the signal — "someone genuinely wanted this" — turns into noise.

The governor is a thermostat enforcing that rule:

- **Token price doubles?** → Coins-per-can halves. Your reward is fewer coins, but each is worth more — your *rupee* subsidy stays ~₹20.
- **Token price crashes?** → Coins-per-can rises (up to a hard cap), keeping the subsidy meaningful so miners don't abandon the network.

Read the formula as a sentence:

> *"Total rupee value handed out each epoch ≈ ₹20 × number of verified cans. The token price only decides how many coins it takes to deliver those ₹20."*

Two profound consequences:

**(a) Price gains go to holders, not miners.** The marginal miner always earns the same real subsidy. All appreciation flows to people *holding* coins. This is what keeps Goodhart's Law caged — no token pump can ever make chugging soda profitable.

**(b) The supply cap is reflexive.** Look what happens as price rises without bound:

```
as p̄ → ∞ ,   Eₜ → 0
```

Emission doesn't just have a ceiling — it *strangles itself* as the coin succeeds. The more valuable DietCoin becomes, the scarcer new issuance gets, asymptotically freezing total supply. The asset **hardens as it appreciates** — like Bitcoin's halvings, except triggered by the coin's own market success rather than a calendar.

---

## 3. The Fundamental Anchor: What Price *Should* Gravitate Toward

### Why scarcity alone is not enough

A common trap: *"verification is solid + supply is limited ⇒ price must be high."*

False. The crypto graveyard is full of perfectly capped, perfectly worthless tokens. Solid verification creates **credibility of scarcity** — it proves supply can't be faked. It does not create demand. Value needs a reason.

DietCoin's reason is the **buy-and-burn**: the DAO sells the consumption data feed and uses the revenue to buy DTC on the open market and destroy it. Every rupee of data revenue becomes permanent scarcity for remaining holders — a dividend paid in scarcity instead of cash.

### The math

| Symbol | Meaning | Toy value |
|---|---|---|
| B | Annual buy-and-burn spend (data revenue) | ₹20 Cr |
| S | Circulating supply | 5 Cr coins |
| r | Discount rate (India risk-adjusted) | 18% |
| δ | Demurrage rate (idle-token decay) | 6%/yr |
| g | Expected growth of data revenue | 12%/yr |

Fundamental value per coin:

```
p_f ≈ B / ( (r + δ − g) · S )
```

Toy numbers: `p_f ≈ 20Cr / (0.12 × 5Cr) ≈ ₹33 per coin.`

### The intuition

This is a dressed-up version of the oldest question in finance: *"what is a stream of future payments worth today?"* (a Gordon growth model, if you want the textbook name).

Piece by piece:

- **B / S** — the yearly burn spread across every coin. Think of it as each coin's "salary": with ₹20 Cr burned and 5 Cr coins, every coin effectively earns ₹4/year of scarcity-value.
- **Divide by (r + δ − g)** — this converts the yearly salary into a lifetime price tag:
  - **r (impatience):** ₹4 next year is worth less than ₹4 today. Higher impatience → lower price.
  - **δ (leakage):** demurrage slowly melts idle coins, so holding costs a little each year — like a small maintenance fee on a flat, slightly lowering what you'd pay for it.
  - **g (growth):** if data revenue grows 12% a year, the salary keeps rising — like a flat whose rent rises every year, which makes it worth *more* today. Growth pushes the price up, which is why g enters with a minus sign.

One-sentence version:

> *"A DietCoin is worth its share of all the future data money that will ever be burned for it — shrunk for impatience and melt, boosted for growth."*

The market will speculate wildly around p_f — that's fine and expected. But arbitrage keeps yanking the drift back toward this anchor: if price sits far below p_f, the burn is buying coins cheap and holders are getting scarcity at a discount, which attracts buyers; far above, the yield is thin and sellers appear. **Grow B tenfold and p_f follows tenfold.** The protocol's real price strategy is not tokenomics — it's growing data revenue.

---

## 4. Floors, Collars, and the (Deliberate) Absence of a Ceiling

### The honest floor hierarchy

Be precise here, because DietCoin's floor is *weaker* than Bitcoin's, and pretending otherwise is dangerous:

| Level | What it is | Strength |
|---|---|---|
| **Hard floor** | Zero | Absolute — nothing stops price from going there if demand dies |
| **Soft floor** | p_f from the burn flow | Strong *while data revenue exists* |
| **Cost-of-production floor** | Nearly none | For someone who'd drink the can anyway, marginal minting cost ≈ 30 seconds of effort ≈ ₹0. (For a pure *farmer*, cost per coin = (c − their enjoyment value)·V/E, which the governor keeps *above* market price by construction — so farming never puts in a floor either; it just never happens.) |

Bitcoin's price is propped up by billions in electricity that miners must recoup. DietCoin has no such prop. Its floor is the data business, full stop.

### The collars (rate limiter, not ceiling)

```
Each batch clears within ±10% of the previous print.
Excess order imbalance rolls into the next batch.
```

**Intuition:** think of a circuit breaker on a stock exchange, miniaturized to 5-minute steps. The price can still move 10× in a day if the move is genuine — sustained demand just keeps pushing batch after batch. But a manipulator trying to spike one print faces a wall: their giant order only moves the price 10% and the rest spills into the next auction, where honest flow dilutes it. Manipulation becomes a slow, expensive grind instead of a single dramatic candle. The index gains the statistical smoothness a macro instrument needs — jagged, spoofable prints would disqualify DietCoin from ever sitting next to the PMI on a terminal.

### Why no ceiling — and why that's the whole point

Your instinct is correct: **there is no ceiling, and there must never be one.**

- Price is bounded above only by (a) demand for the data yield and (b) the cultural/belief premium — both can grow without limit.
- The governor already neutralizes the only danger of a high price (profitable over-consumption) by shrinking emission as price rises.
- And strategically: the entire ambition — DietCoin as a defining economic parameter for urban India — lives in the **uncapped right tail**. A ceiling would amputate the one outcome worth building for.

---

## 5. The Whole Algorithm in One Box

```
┌─────────────────────────────────────────────────────────────┐
│  MARKET computes:                                           │
│     p* = argmax min( D(p), S(p) )     every 5 minutes       │
│     (subject to ±10% collar per batch)                      │
│                                                             │
│  PROTOCOL computes (only the quantity dial):                │
│     Eₜ = min( E_max , θ·c·Vₜ / p̄ₜ )                         │
│     → constant real subsidy per can (≈ ₹20)                 │
│     → emission → 0 as price → ∞  (reflexive hardening)      │
│                                                             │
│  GRAVITY comes from:                                        │
│     p_f ≈ B / ((r + δ − g)·S)                               │
│     → grow the data revenue B, and the price follows        │
│                                                             │
│  BOUNDS:                                                    │
│     Floor: zero (hard), p_f (soft) — no production floor    │
│     Ceiling: none, by design                                │
└─────────────────────────────────────────────────────────────┘
```

**One line:** the market computes the price; the protocol computes only the quantity; the data burn supplies the gravity; the collars supply the smoothness — and the ceiling-free right tail is where the "economic parameter for urban India" outcome lives.
