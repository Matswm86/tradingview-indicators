# LiqSweep+iFVG Indicator

Pine Script v6 indicator for intraday futures. Built and tested on MNQ (Micro E-mini Nasdaq-100), 1-minute timeframe.

![LiqSweep+iFVG on MNQ 1m](assets/liqsweep-ifvg-mnq-1m.png)

Session-liquidity raid into inverted-FVG reversal.

- Tracks each session's high and low (New York, London, Asia) as liquidity levels; a level dies on its first touch, and a new session's level replaces the previous one of the same type. An input ("Untouched levels kept live per session type") optionally keeps the last N sessions' untouched levels live simultaneously, so an older untouched pool can still be raided days later.
- A raid beyond a tracked level (by a configurable buffer) arms a reversal in the opposite direction for a limited window.
- Entry signal: a Fair Value Gap gets body-closed through its far edge against the raid direction (iFVG inversion). One inversion clears every live arm of that direction: one signal per reversal. A gap formed before the sweep can invert; only the inversion has to happen after the raid.
- Optional modules: equal highs/lows tracking (EQH/EQL, CantoLab "EQ & RE" structure) with optional raid-arming, a higher-timeframe FVG confluence filter (require price inside a live higher-TF gap; 5m / 15m / 1h / 4h / daily) with optional gap overlay, session boxes, raid labels, the Macro Dealing Range premium/discount zones (the standalone indicator below, built in as a group 05 toggle, on by default), and Williams-fractal swing-point marks (group 06, default 10 periods).
- Signal timing validated on 60 days of real MNQ 1-minute data: the entry triangle prints on the inversion bar (enter next bar).

**File:** [`LiqSweep_iFVG.pine`](LiqSweep_iFVG.pine). Paste into TradingView's Pine editor. Works on any timeframe; defaults were tuned and validated on MNQ 1-minute. Signals print on the inversion bar (enter next bar).

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

## Disclaimer

These are chart tools, not trading advice. Nothing here is a recommendation to buy or sell anything. Test everything yourself before risking money.

## License

[MIT](LICENSE)
