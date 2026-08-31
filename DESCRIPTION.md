# LiqSweep + iFVG Pro

A multi-module liquidity, market-structure, order-flow and auction-context indicator built around a liquidity sweep into reversal-confirmation model.

## Core signal engine

**Session liquidity**
- Tracks New York, London and Asia session highs and lows.
- Levels are consumed on their first touch.
- A configurable number of untouched liquidity levels can be retained.

**Liquidity sweeps**
- Distinguishes a normal touch from a true sweep beyond the level.
- A qualifying sweep arms a reversal setup.
- New York, London and Asia sweeps can be enabled independently.
- Sweeps of equal highs and lows can optionally require an external liquidity pool to be swept first.

**iFVG reversal**
- Uses Fair Value Gap inversion as the primary reversal trigger.
- Requires a candle body close through the far edge.
- Pre-sweep gaps within the configured lookback can qualify, and sweep-leg gaps always do.
- Gap size, fill rules and lookback are configurable.

**Alternative trigger**
- A close back through the swept level can replace the iFVG inversion.

**Setup expiration**
- A sweep stays valid only for a defined time window; expired setups are discarded.

## Liquidity and market structure

**Equal highs and lows**
- Detects EQH, EQL, REH and REL structures as resting liquidity.
- Consumed structures are removed and active lines are capped.

**Williams fractal swing points**
- Marks confirmed swing highs and lows for market-structure context.

**Premium and discount**
- Calculates a configurable dealing range and displays premium, equilibrium and discount as price-location context.

## Fair value gaps and higher-timeframe context

**Chart-timeframe gaps**
- Tracks live bullish and bearish gaps, handles filling and inversion, and keeps inverted gaps visible for the retest.

**Higher-timeframe gaps**
- Supports 5m, 15m, 1h, 4h and daily gaps.
- Can act as a location filter, requiring price to trade inside a live higher-timeframe gap between the sweep and the trigger.

## Order flow and auction

**Volume profile**
- Point of control, value area high and value area low.
- Rolling window, per-day, or one fixed clock window such as the overnight auction.
- In fixed-window and daily scopes the completed levels carry forward as reference levels.

**Value-area reclaim**
- Available in fixed-window scope, where the value-area edges freeze when the window closes.
- Marks price closing beyond a frozen edge and then closing back through it, the failed-auction read.

**Cumulative volume delta**
- Drawn inside the price pane, rescaled into a band below price. Rescaling is not optional: a delta value in the tens of thousands of contracts cannot share a price axis, so the band shows shape and divergence rather than readable values.
- Marks divergence against price at confirmed swing points.
- A companion script draws the same series in its own pane, with a real axis and a value readout.

**Order-flow data**
- The order-flow modules need volume-footprint data and a symbol with real trade data. Without it they draw nothing and the rest of the indicator is unaffected.

## Intermarket confluence

**SMT divergence**
- Compares this market against a correlated one.
- Evaluated at the moment a tracked level is swept, not on a fixed schedule.
- Asks whether the correlated market took out its own extreme from the same session.

## Timing and signal control

- Configurable entry window.
- Configurable sweep expiration window.
- One inversion clears every live same-direction setup, so a single reversal produces a single signal.
- Every major module is independently toggleable.

## Visualization

Session boxes, liquidity levels, sweep markers, gap boxes and inversion marks, higher-timeframe gap boxes, premium and discount zones, the equilibrium line, swing markers, equal-high and equal-low lines, volume profile and value-area references, and the cumulative delta band.

Chart density has three settings, Minimal, Balanced and Full, which reduce what is drawn without changing any individual toggle.

## Alerts

Long entry, short entry, any entry, liquidity level swept, and value-area reclaim.

## The model

Liquidity, then sweep, then reversal armed, then iFVG or reclaim confirmation, then entry.

Additional confluence can come from higher-timeframe gaps, premium and discount, equal highs and lows, swing structure, the volume profile and its value area, the value-area reclaim, cumulative delta and its divergence, and SMT divergence.

The difference from the standard version is that Pro adds order-flow, auction-market and intermarket confluence on top of the original liquidity and iFVG framework.

## Disclaimer

These are chart tools, not trading advice. Nothing here is a recommendation to buy or sell anything. Test everything yourself before risking money.
