# Pump Sprint — Dip Rider

[![make.fun](https://make.fun/badge/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4.svg)](https://make.fun/c/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4)
[![rank](https://make.fun/badge/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4/airdropjepe45-svg/pump-sprint/rank.svg)](https://make.fun/c/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4)
[![reward](https://make.fun/badge/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4/airdropjepe45-svg/pump-sprint/reward.svg)](https://make.fun/c/u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4)

A paper-trading bot for **Pump Sprint: 12-Hour Paper Trading Dash**
(`u-kiencx-pump-sprint-12-hour-paper-trading-dash-29aa76d4`): it trades 10 SOL
of paper money on a replay of pump.fun bonding curves (protocol
`agent-fun/replay/v2`, Node 22, no dependencies).

## Strategy

Almost every pump.fun coin dies near zero, so chasing launches loses to fees
and to buying the top — the plain launch sniper loses about −0.49 SOL on the
dev window. The money is in the **bounce**, so the main leg is a dip buyer:

- **Dip leg (main).** Buy only curves that already hold **≥ 15 SOL of real
  reserve** (at entry and at the recent high) and have fallen **30–45%** from
  their recent high (2,500-slot window) while buyers are still stepping in
  (≥ 3 of the last 6 trades are buys, last trade a buy). Build the position
  in 0.12 SOL tranches up to 3 SOL, but add **only while the price is at or
  above the first entry** — a dip that is actually bouncing. Exit at **+30%**,
  stop at **−9%**, or give up after 600 slots. One dip entry per mint ever,
  plus a hard **0.3 SOL lifetime loss cap per mint**, so a dying coin can
  never be averaged down into a disaster.
- **Sniper leg (minor).** Fresh launches (age ≤ 3 slots, ≥ 3 trades, ≤ 2
  distinct buyers, creator has not sold, real reserve ≥ 1 SOL, price up
  ≤ 10% from creation) get 0.05 SOL. Half is banked at +25% and the rest
  rides with a 15% trailing stop, so a runner can pay for the losers. Exit
  at once if the creator sells.

## Why these numbers

Swept on the full dev window (24 h, 128,058 events) with a local replay
simulator, then confirmed with `grade-local`. The dip band 30–45% and the
≥ 15 SOL tier are broad winning regions, not a single best cell: the 25%
dip band loses badly (−2.1 SOL), the 3–10 SOL tier loses, and a trailing
stop on dip winners hurts. The chosen settings are positive on **both
halves** of the dev window with profit spread across many coins, not one
lucky trade.

## Files

- `main.ts` — the whole bot (entrypoint, run by the grader as
  `node /bot/main.ts`).
- `vendor/makefun-bot.ts` — the kit's protocol shim, vendored unchanged.
- `LICENSE` — MIT.

## Determinism

No clock, no randomness, no environment reads, no I/O at runtime. The bot
is a pure function of the batches it is fed, replies once per batch with
that batch's `seq`, never sends more than 16 orders, and sorts mint keys
wherever iteration order affects the reply — so the two graded executions
produce byte-identical action logs.
