# Social Media Rollout Plan

**Paper:** Quantifying Bitcoin Network Resilience Through Critical Scenario Discovery
**Workshop:** University of Wyoming Bitcoin Research Initiative, July 13–17, 2026
**Author:** Peter Foytik, Old Dominion University (pfoytik@odu.edu)

---

## Platform Strategy

| Platform | Audience | Content Style |
|---|---|---|
| X/Twitter | Bitcoin community, miners, developers | Threads, visuals, real-time workshop posts |
| Nostr | Bitcoin-native audience | Cross-posts from X; miner dynamics findings resonate here |
| LinkedIn | Academic, institutional, grant-relevant | Phase 3 deep dives, governance framing |
| Reddit (r/BitcoinDiscussion) | Engaged researchers and enthusiasts | Pool identity and "what doesn't matter" findings |

---

## Phase 1: Pre-Workshop Buildup
**Dates:** June 30 – July 11
**Goal:** Establish the research exists and build anticipation before the workshop.

### June 30 — Announcement

> "We ran 2,694 Bitcoin fork simulations on real bitcoind nodes using Warnet. Here's what we learned about when a soft fork succeeds and when it fails — full thread coming at the UW BRI workshop July 13–17. Data and code: [GitHub links]"

**Platforms:** X/Twitter, Nostr, LinkedIn

---

### July 3 — The Economic Self-Sustaining Point (F10, F14)

> "Below 74% economic adoption, no amount of miner support can save a soft fork. Above 82%, miners can't stop it either. The contested zone is remarkably narrow — and most governance debates happen entirely within it."

**Findings referenced:** F10 (ESP at E≈0.74), F14 (economic override total at E≥0.82)
**Platforms:** X/Twitter, Nostr

---

### July 7 — The 2016-Block Retarget Cascade (F11)

> "The 2016-block difficulty adjustment is the secret weapon of Bitcoin fork dynamics. Whoever mines 2016 blocks first gets 2–3× more profitable blocks instantly — and that single event cascades everything. Here's the mechanism."

**Findings referenced:** F11 (retarget as cascade trigger)
**Platforms:** X/Twitter, Nostr, LinkedIn

---

### July 11 — Pool Identity Teaser (F18, F19)

> "Foundry alone committing to a fork produces a different outcome than 5 smaller pools with the same aggregate hashrate. It's not how much hashrate — it's whose hashrate. Presenting the mechanism at the workshop in 2 days."

**Findings referenced:** F18 (Foundry identity proxy), F19 (pool composition identity)
**Platforms:** X/Twitter, Nostr, Reddit

---

## Phase 2: Workshop Live
**Dates:** July 13–17, 2026 (UW Bitcoin Research Initiative)
**Goal:** Real-time engagement at peak attention. Share visuals from the presentation.

### July 13 — Arrival Post

> "At the University of Wyoming Bitcoin Research Initiative workshop! Presenting Bitcoin fork simulation research on Wednesday. Code and data available now: [GitHub links]"

**Platforms:** X/Twitter, Nostr, LinkedIn

---

### Presentation Day — Live Thread

Post 3–4 key visuals during or immediately after the presentation:

1. **The two-scale decision boundary plot** — economic_split globally, pool_committed_split locally
2. **The retarget cascade diagram** — mechanism from first retarget to full pool switch
3. **The pool identity result** — Foundry alone vs. equivalent smaller pools
4. **The parameter importance table** — what matters and what doesn't

**Platforms:** X/Twitter, Nostr (primary amplification window)

---

### Day After Presentation — The "Is This Real?" Post

> "The question I got asked most at the workshop: 'Is this modeling or could this actually happen?' Here's why I think the mechanisms are real — and why Foundry's hashrate concentration is the most underappreciated variable in Bitcoin governance."

**Platforms:** X/Twitter, LinkedIn, Reddit

---

## Phase 3: Weekly Deep Dives
**Dates:** July 21 – late August
**Goal:** Sustained engagement, one finding cluster per week. Each post can be a thread on X and a standalone article framing on LinkedIn.

---

### Week 1 (July 21) — The Two-Scale Structure
**Findings:** F8, F15

> "Bitcoin fork outcomes are governed by two nested scales. At the global level, economic adoption (who holds BTC) determines whether the fork is even contested. Within the contested zone, miner commitment and the 2016-block race decide the winner. These aren't contradictory — they describe the same boundary at different zoom levels."

**Visual:** Decision boundary plot (E vs C plane)
**Platforms:** X/Twitter thread, LinkedIn article, Nostr

---

### Week 2 (July 28) — What Doesn't Matter
**Findings:** F2, F4, F6, F9

> "We tested 10 parameters across 2,694 scenarios. 6 of them have zero effect on fork outcomes. Neutral miner percentage, hashrate split, solo miner hashrate, profitability threshold, user inertia, economic switching threshold — all confirmed non-causal. Here's what you can safely ignore in the next governance debate."

**Platforms:** X/Twitter thread, Reddit (r/BitcoinDiscussion), Nostr

---

### Week 3 (August 4) — The Economic Dead Zone
**Findings:** F10, F14, F12

> "Above 82% economic adoption, nothing miners do matters — the fork wins regardless. Below 50%, it fails regardless. In between is a 32-percentage-point window where mining dynamics actually matter. Most soft fork governance debates happen entirely in this window."

**Visual:** Economic override threshold plot
**Platforms:** X/Twitter, LinkedIn, Nostr

---

### Week 4 (August 11) — Pool Identity vs. Aggregate Hashrate
**Findings:** F18, F19, F20

> "The standard model of fork dynamics treats miner hashrate as a fungible scalar. It isn't. A single large pool committed to a fork creates a faster retarget race than multiple smaller pools at the same aggregate hashrate. Foundry at 27% is not the same as AntPool + ViaBTC at 27%. Here's the mechanism."

**Visual:** Pool composition grid (win rate by which specific pools commit)
**Platforms:** X/Twitter thread, Reddit, Nostr, LinkedIn

---

### Week 5 (August 18) — The Two-Layer Cascade
**Findings:** F11, F16, F17

> "Winning the hashrate war is not the same as winning the fork. 81% of scenarios where Fork A wins the mining competition do NOT produce full economic migration. The pool layer and the economic layer are operationally decoupled — and full adoption requires a specific cascade sequence that most hashrate wins never trigger."

**Platforms:** X/Twitter thread, LinkedIn, Nostr

---

### Week 6 (August 25) — Governance Implications
**Alignment:** BCAP framework

> "What do 2,694 fork simulations tell us about Bitcoin governance? The data maps cleanly onto the Blockchain Consensus Analysis Protocol (BCAP). The key insight: governance debates that focus on miner signaling thresholds are fighting over the wrong variable. Economic adoption is what decides contested forks — and it's nearly impossible to measure in real time."

**Platforms:** LinkedIn (primary), X/Twitter, Nostr

---

## High-Engagement Posts to Watch

These two findings are the most likely to reach beyond research circles:

1. **Pool identity finding (F18/F19)** — counterintuitive, directly relevant to anyone watching Foundry's market position. Expect discussion from miners and mining pool operators.

2. **"6 parameters don't matter" (F2/F4/F6/F9)** — clear, surprising, and directly challenges assumptions in common governance discourse. Reddit and X will engage with this.

---

## Links to Include in Every Post

- Research companion repo: `https://github.com/pfoytik/bitcoin-fork-governance-study`
- Community framework repo: `https://github.com/pfoytik/warnet-fork-experiments`
- Zenodo data deposit: *(add DOI once registered)*
- Paper: *(add link once published)*
