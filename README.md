# Air-Mix Pict Chance Draw

A web-based 3D physics simulation of a 6/45 air-mix lottery drawing machine. The only input is the
**order in which the balls are loaded** into the machine; a fixed-timestep rigid-body simulation then
draws 6 numbers plus 1 bonus by default (configurable). The same loading order always produces the same numbers.

Demo: open `index.html` in a browser (the only dependency is Three.js r128 from cdnjs).
Shareable Claude artifact: https://claude.ai/artifact/Y17gnniYFi5Rc3AyJusXQi

## How it works

0. (Optional) **Ball setup**: choose the number of balls (8–99) and the number printed on each ball
   (up to 4 characters, e.g. `101` or `A7`), and how many balls are drawn (1–12 winning balls, with or without
   a bonus ball). The setup is saved in the browser; "Pict Chance default" restores 6 + 1 of 45.
   The physics and the seed only see ball positions 1…N, so the default 45-ball setup replays exactly as before
1. Pick the balls in the order they go into the machine (type them, fill ascending, or fill shuffled)
2. Start the draw → balls drop in through the top tube → 5 s of air mixing
3. Blower pauses 1.2 s so the funnel empties → blower restarts and the gate opens at the same moment →
   a ball carried up the jet into the inlet funnel and through the throat is drawn, and the gate closes.
   Repeats once per drawn ball (default 7: 6 sorted + bonus)
4. "Draw again with the same order" verifies the replay
5. When the seventh ball lands, an overlay shows the winning numbers in large type with a copy button
   (numbers, draw order, seed, loading order and page URL)

## 64k-intro techniques

- **Single seed**: the loading order is hashed with 32-bit FNV-1a and seeds every procedural element
- **Integer-hash value noise**: no `sin/cos/exp`; only `Math.imul`, basic IEEE arithmetic and `sqrt`, so results replay identically across browsers
- **Fixed timestep** `dt = 1/240 s`: frame rate and speed multiplier only change how many steps run per frame, never the outcome
- **Air field** = central rising jet (rational-function core, weakening with height) + floor inflow + 3D noise turbulence; balls follow it through linear drag `a = k(u − v)`
- **Collisions**: sphere–sphere impulses (e = 0.55) with position correction, glass shell, both faces of the inlet funnel (signed cone distance), throat wall, gate lid
- **The draw is pure physics**: no artificial suction. The jet's drag pushes balls up into the funnel and only a ball that passes the throat is drawn, so draw timing depends on the mixing state
- **Procedural assets**: ball textures are drawn on a canvas at runtime, all geometry is primitives, zero external asset files
- **Procedural sound**: synthesized with Web Audio at runtime (zero sample files). Ball-ball and ball-glass impacts scale with impact speed and pan with the camera, the blower hum fades with the air jet, a rising whoosh when a ball is drawn, a bell note climbing per tray slot, and a fanfare when all seven are out

## Controls

- 1× / 2× / 4× / 8× speed, drag to orbit, wheel to zoom, auto-orbit toggle, sound on/off (saved in the browser)
