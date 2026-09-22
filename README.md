# TradingView Indicators

Pine Script v6 indicators for intraday futures, built and tested on MNQ (Micro E-mini Nasdaq-100).

| Indicator | File | Idea |
|---|---|---|
| EMA x VWAP Engulfing | `EMA_VWAP_Engulfing.pine` | Fast EMA against session VWAP, five setups, a twelve-point confluence score |
| LiqSweep+iFVG | `LiqSweep_iFVG.pine` | Session-liquidity raid into inverted-FVG reversal |
| LiqSweep+iFVG Pro | `LiqSweep_iFVG_Pro.pine` | Same core plus order-flow and auction confluences |
| LiqSweep+iFVG Pro +VWAP/Fib | `LiqSweep_iFVG_Pro_VWAP_Fib.pine` | Pro plus anchored VWAP and fib-retracement confluence |
| LiqSweep CVD | `LiqSweep_CVD.pine` | Cumulative volume delta companion pane |
| Liquidity Map | `Liquidity_Map.pine` | Engineered liquidity, respected levels and live liquidity blocks |
| LSD Model | `LSD_Model.pine` | Supply/demand zone + liquidity sweep + directional-close entry |
| Trend Hub | `Trend_Hub.pine` | Qualified-trend and momentum read across three timeframes |
| Session Pulse | `Session_Pulse.pine` | Live session volume pace and how much of a normal day's range is spent |

## EMA x VWAP Engulfing

Watches one fast EMA against a session VWAP, marks the bar where a confirmation candle
turns that relationship into a trade, and scores the context behind every mark so a thin
signal looks thin instead of looking exactly like a strong one.

- **The map.** Above both the EMA and VWAP is buyer control, below both is seller control,
  between the two lines is a chop zone the indicator refuses to trade by default.
- **Five setups, each with a switch.** A: the EMA crosses VWAP. B: a trend pulls back into
  the EMA and holds it. C: price stretches far from VWAP and turns back. D: price pulls back
  into VWAP and rejects it. E: price rejects the 1-sigma VWAP band and fades to VWAP.
  A, B and D ship on; C and E buy under VWAP and sell over it, so both ship off.
- **Four confirmation shapes.** A body engulfing bar, a rejection wick at VWAP or the EMA,
  a momentum bar that pulls back to the EMA and closes beyond the previous bar, and a tag
  of the 1-sigma band. The momentum shape exists because the others are structurally
  impossible inside a one-way expansion: an engulfing bar needs an opposite-coloured candle
  to swallow, and a rejection wick needs price to still be near VWAP.
- **A score, not a chain of gates.** Twelve context checks each pay a point, including
  volume against the same slot of the session on earlier days, VWAP slope, an EMA that is
  still travelling, minutes held on one side of VWAP, the higher timeframe, and the side of
  a slow third EMA. Every signal is graded A+, A or B. Five checks can be promoted from a
  point to a hard veto and four are by default.
- **Day quality.** Two session-level gates stand the indicator down on a day trading well
  below its usual pace or travelling well below its usual range by that point of the
  session, which no per-bar threshold catches.
- **Levels die when taken.** The previous day's high and low, the pre-market pair and the
  Asia pair leave the chart and stop paying their confluence point once a bar trades
  through them.
- **A dashboard that explains silence.** A plain verdict line, a three-timeframe trend
  block, one row per check with an ok or no beside it, and a named biggest blocker for the
  session, so a quiet chart tells you which number is too tight.

## LiqSweep+iFVG

![LiqSweep+iFVG on MNQ 1m](assets/liqsweep-ifvg-mnq-1m.png)

Session-liquidity raid into inverted-FVG reversal.

- Tracks each session's high and low (New York, London, Asia) as liquidity levels; a level dies on its first touch, and a new session's level replaces the previous one of the same type. An input ("Untouched levels kept live per session type") optionally keeps the last N sessions' untouched levels live simultaneously, so an older untouched pool can still be raided days later.
- A raid beyond a tracked level (by a configurable buffer) arms a reversal in the opposite direction for a limited window.
- Entry signal: a Fair Value Gap gets body-closed through its far edge against the raid direction (iFVG inversion). One inversion clears every live arm of that direction: one signal per reversal. A gap formed before the sweep can invert; only the inversion has to happen after the raid.
- Optional modules: equal highs/lows tracking (EQH/EQL) with optional raid-arming, a higher-timeframe FVG confluence filter (5m / 15m / 1h / 4h / daily) with optional gap overlay, session boxes, raid labels, premium/discount dealing-range zones, and Williams-fractal swing-point marks.
- Signal timing validated on 60 days of real MNQ 1-minute data: the entry triangle prints on the inversion bar (enter next bar).

## Pro version

[`LiqSweep_iFVG_Pro.pine`](LiqSweep_iFVG_Pro.pine) is the same core with order-flow and auction confluences layered on. Everything is toggleable, and a single "Chart density" control (Minimal / Balanced / Full) strips the chart back without touching the individual switches.

![LiqSweep+iFVG Pro on MNQ 5m](assets/liqsweep-ifvg-pro-mnq-5m.png)

- **Volume profile** built from `request.footprint()`: point of control and value-area edges, over a rolling window, per day, or over one fixed clock window (18:00-08:55 New York by default, the overnight auction) whose edges freeze and carry into the session.
- **Value-area reclaim** markers: price closes beyond a frozen value-area edge and then closes back through it, the failed-auction read.
- **Cumulative volume delta** drawn as a rescaled strip inside the price pane, with divergence marked at confirmed swings. [`LiqSweep_CVD.pine`](LiqSweep_CVD.pine) draws the same thing in its own lower pane, since one Pine script only gets one pane.
- **SMT divergence** against a correlated market, evaluated only where a tracked level is swept, comparing that market's own extreme over the same session.
- **Sequencing rule**: optionally require an external pool (a session high or low) to be swept before a sweep of an equal high or low is allowed to arm anything.
- **Alerts** for long, short, any entry, level swept, and value-area reclaim, so the markers can be switched off entirely.

The footprint modules need a TradingView plan that includes volume-footprint data, and a symbol with real trade data rather than a CFD proxy. Where footprint data is unavailable those modules draw nothing and the rest of the indicator is unaffected.

[`LiqSweep_iFVG_Pro_VWAP_Fib.pine`](LiqSweep_iFVG_Pro_VWAP_Fib.pine) is the Pro build with anchored-VWAP and fib-retracement confluence added on top. It is the largest file here and the slowest to load; run it on one chart, not four.

## Liquidity Map

[`Liquidity_Map.pine`](Liquidity_Map.pine) draws the liquidity taxonomy the way it is taught by
InterEquity (Marco Accettone), Photon Trading, RealTraderTim, Zamco and the Mind Math Money
course, rather than inventing a new one.

- **Respected levels**: a later pivot touches an earlier pivot without exceeding it and moves away.
- **Equal highs and lows** inside a relative-equal band, with the touch count in the label.
- **Engineered levels**: a respected level sitting short of an intact higher-timeframe reference,
  which is where stops get built rather than where price is going.
- **Liquidity blocks** as live, validated, promotable objects: an anchor for the entry and the
  stop that dies only when its target pool is taken or price closes through it, and that gets
  promoted when a new high respects it and traps early sellers.
- **Focus mode**, on by default, shows engineered liquidity, its draw and respected levels, and
  puts everything else behind a toggle.

## LSD Model

[`LSD_Model.pine`](LSD_Model.pine) implements the "LSD" (Liquidity + Supply/Demand) model taught publicly by Mangoe (mangoe.co playbook and YouTube), coded to the 5-minute futures practice rather than the older 30-minute forex rules.

![LSD Model on MNQ 5m](assets/lsd-model-mnq-5m.png)

- **Zone**: the final opposite-direction candle before an impulsive move (N straight candles, displacement ≥ k×ATR), extended to the next candle's near wick. Optional "accuracy zone" trimming for forex (off by default; the model's author reports it works worse on futures).
- **Liquidity**: a 2+ candle swing must form in front of the zone without touching it, in the zone-side half of the setup (fib 50% rule), then structure must break in the setup's direction.
- **Entry signal**: price sweeps the liquidity, wicks into the zone without a body close inside it, and the first directional close prints the triangle. Stop line at the deepest wick into the zone, targets at 1:3 and 1:4.
- **Structure age**: a zone expires nine hours after its base candle, measured from the base rather than from the detection bar.
- **Grading**: each signal carries an A, B or C grade from tap depth, tap volume, approach shape and higher-timeframe agreement.
- **Session filter** and four alerts: long signal, short signal, zone tapped, liquidity swept.

## Trend Hub

[`Trend_Hub.pine`](Trend_Hub.pine) is a corner panel, not a signal generator. It reads a qualified
trend on three timeframes at once (5m / 15m / 4H by default): a swing point is actualised after
six bars, a close through the last swing flips the direction, and the volume at that break decides
whether the flip is confirmed or merely suspect. Each row shows direction, a strength meter and an
RSI momentum read.

**The 3/3 alignment row is not an entry trigger.** The panel tells you which way the larger
clock is running so you can size and time a trade you already have a reason to take. Trading the
alignment itself is the mistake it is designed to prevent.

## Session Pulse

[`Session_Pulse.pine`](Session_Pulse.pine) is a lower pane that answers two questions about the day
you are actually in.

- **Volume pace**: how much the session has traded so far against a typical session at the same
  point, drawn as a strip whose thickness and glow carry the magnitude.
- **Room left**: how much of a typical day's range is already spent, from running session extremes
  only, against an average of the last 20 completed session ranges. Teal means room to travel, red
  means a fresh reversal has little to reach for.

No predictive claim is attached to either metric.

## Disclaimer

These are chart tools, not trading advice. Nothing here is a recommendation to buy or sell anything. Test everything yourself before risking money.

## License

[MIT](LICENSE)
