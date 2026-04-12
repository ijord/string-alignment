# DIY Alignment Calculator

## Project Overview
A standalone mobile-friendly HTML page (`index.html`) for performing a DIY string-based wheel alignment. No server or build step required — open directly in a browser. All data is persisted via `localStorage`.

## File
- `index.html` — the entire app (HTML + CSS + JS in one file)

## What It Does
Accepts string-to-wheel-edge measurements for all four wheels, calculates individual wheel toe angles, total axle toe, and thrust angle. Optionally calculates front/rear track width if string width is provided.

## Input Variables
| ID | Description | Default |
|----|-------------|---------|
| A | Front-left wheel, front edge to left string | 100 |
| B | Front-left wheel, rear edge to left string | 100 |
| C | Rear-left wheel, front edge to left string | 100 |
| D | Rear-left wheel, rear edge to left string | 100 |
| E | Front-right wheel, front edge to right string | 100 |
| F | Front-right wheel, rear edge to right string | 100 |
| G | Rear-right wheel, front edge to right string | 100 |
| H | Rear-right wheel, rear edge to right string | 100 |
| I | Front wheel diameter (at rim measurement point) | 550 |
| J | Rear wheel diameter (at rim measurement point) | 550 |
| K | Wheelbase | 3000 |
| SS | String width (optional, for track width calc) | blank |

Defaults apply only on the very first visit (no localStorage data) for the Before panel. The After panel only stores A–H (it mirrors I/J/K/SS from Before). SS is intentionally left blank so track width doesn't calculate until explicitly entered.

## Calculations (from Rob Robinette's DIY Alignment Calculator)
```
hub_center = (front_edge + rear_edge) / 2

thrust_angle (r) = asin((FL_hub - FR_hub - RL_hub + RR_hub) / (2 * K))

FL_toe = asin((A - B) / I) - r
FR_toe = asin((E - F) / I) + r
RL_toe = asin((C - D) / J) - r
RR_toe = asin((G - H) / J) + r

total_front_toe = FL_toe + FR_toe
total_rear_toe  = RL_toe + RR_toe

track_diff = (FL_hub + FR_hub - RL_hub - RR_hub) / cos(r)

front_track = SS - FL_hub - FR_hub   (only when SS entered)
rear_track  = SS - RL_hub - RR_hub   (only when SS entered)
```

Positive toe = toe-in, negative = toe-out.

## Display / Visual Design
- **Color scheme**: Light background (`#f0f4f8`), white cards full screen width, dark slate text
- **Toe-in color**: Cyan (`#0891b2` text, `#06b6d4` canvas)
- **Toe-out color**: Magenta (`#a21caf` text, `#d946ef` canvas)
- **Amplification**: Tire and thrust graphics are drawn at **100× amplification**, capped at ±45° for display
- **Angles**: Displayed to hundredths of a degree (x.xx°)
- **Track widths**: Displayed to tenths (x.x)

## Layout Structure

Three swipeable panels (mobile) or side-by-side columns (desktop), navigated via `goTo(idx)`, swipe, or arrow keys:

1. **Before panel** — fully editable inputs: I, J, K, SS + A–H measurements; 5-column layout:
   - Col 1: Left inputs (A/B front, C/D rear) + orange string bar
   - Col 2: Left tire canvases (FL top, RL bottom) + toe value/label
   - Col 3: Center — Front Toe, Thrust angle (mini canvas), Rear Toe
   - Col 4: Right tire canvases (FR top, RR bottom) + toe value/label
   - Col 5: Orange string bar + Right inputs (E/F front, G/H rear)
   - Optional card below: SS input; Front/Rear Track (require SS), Track Difference
2. **After panel** — same layout but I, J, K, SS are read-only displays mirrored from Before; only A–H are editable
3. **Difference panel** — read-only 2×2 grid of per-wheel toe deltas + summary (front toe Δ, rear toe Δ, thrust Δ)

## String Bar Design
The orange string bars (representing physical alignment strings) sit between the input columns and the tire columns on each side, with "STRING" text centered vertically in the bar.

## localStorage
- `diy_align_before` — stores all inputs (A–K, SS) for the Before panel
- `diy_align_after`  — stores A–H inputs for the After panel
- Saved on every input change; defaults applied on first visit (no saved data)
- `diy_align_v1` (legacy) — migrated into Before on first load, then deleted

## Key JS Functions
| Function | Purpose |
|----------|---------|
| `loadValues(sfx, lsKey)` | Load from localStorage or apply defaults; `sfx` is `'b'` or `'a'` |
| `saveValues(sfx, lsKey)` | Save inputs to localStorage |
| `calculate(sfx)` | Main calculation — runs on every input change |
| `calculateDiff()` | Computes Before→After deltas and populates Difference panel |
| `drawTire(canvasId, toeDeg, side)` | Draw 100× amplified tire direction on canvas |
| `drawThrust(canvasId, angleDeg)` | Draw 100× amplified thrust arrow on mini canvas |
| `updateCard(id, sfx, deg, side)` | Update tire canvas + value/label for one wheel |
| `goTo(idx)` | Slide to panel 0/1/2; also handles swipe (>40px) and arrow keys |

## Formulas Reference
Based on: https://robrobinette.com/DIYAlignmentCalculator.htm
