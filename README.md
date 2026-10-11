# The Keeper’s Room

A tiny endless bouncer — one HTML file, zero dependencies.

Bounce up an infinite tower of glowing platforms and see how deep the tide takes you. Every sound is synthesized live in your browser with the Web Audio API; there are no assets to load.

![screenshot](screenshot.png) <!-- optional: drop a screenshot at docs/screenshot.png -->

## Play it

No build step. Just open [`index.html`](./index.html) in any modern browser (desktop or mobile).

Or, if you want a local URL:

```sh
npx serve .
# → http://localhost:3000
```

## Controls

| Input | Action |
| --- | --- |
| `←` / `→` or `A` / `D` | Steer the ball |
| Click & drag (or touch) | Steer toward your pointer |
| `Enter` | Start / start again |
| `R` | Restart instantly |
| `M` | Toggle music |

## How it plays

- You start on a platform and **bounce upward** — each landing gives you a fixed pop, so timing your position over the next platform is everything.
- The score is your **depth**: how far above the surface you’ve climbed. A soft chime marks every 10 depth gained.
- Miss a platform and fall off the bottom of the screen, and it’s game over — one life, no mercy.
- Platforms are always **reachable** from the last one: gaps and horizontal offsets are generated within your maximum arc, so a loss is never just bad luck with the RNG.

## What lives down there

### Shelf types

As you climb, five kinds of glowing shelves appear (each announces itself with a hint the first time you meet it):

| Shelf | Look | Behavior |
| --- | --- | --- |
| Normal | blue-white | A steady bounce |
| Drifting | teal-green | Slides side to side on a sine wave |
| Rising / falling | purple-pink | Bobbing vertically — timing matters |
| Spring | gold | Launches you ~50% higher than a normal bounce, but is narrower |
| Cracked | pale, fissured | Breaks apart after a single bounce |

### Ward wisps (depth 1000+)

Past depth 1000, about **25%** of regular shelves host a stationary wisp — a small turret that charges for 1.5 seconds and fires a bolt at you when you drift within its range. Bolts get faster the deeper you go, scaling to **twice** their base speed by around depth 3300.

### Power-ups (depth 1000+)

Floating pickups appear beside shelves about 12% of the time:

| Pickup | Effect |
| --- | --- |
| Gold diamond | **+50% bounce height for 10 s** (works on springs too) — a timer ring tracks it around your ball |
| Blue shield orb | A pulsing barrier that **absorbs one wisp bolt**, then shatters |
| Gold coin | Every shelf turns golden and bounces like a spring for **5 s** |

Pickups can stack — a boosted gold run is the best way to rocket upward.

### The seven waters (2000-depth cycle)

Every 2000 depth the scene, lighting, and music shift to a new theme:

1. **the Keeper’s Room** — moonlit blue; gentle Am · F · C · G at 74 BPM
2. **the Ember Vault** — warm reds and amber, a smoldering sawtooth hum (84 BPM)
3. **the Neon Tide** — electric purple/pink, the fastest pulse of the set (92 BPM)
4. **the Verdant Deep** — deep greens with triangle-wave pads
5. **the Gilded Halls** — gold and gilt light; stately major keys at 70 BPM
6. **the Frostbound Keep** — icy pale blue-silver; a cold, slow minor drift (64 BPM)
7. **the Ashen Hollow** — monochrome ash and silver; the quietest layer (58 BPM)

…then it loops back to the Keeper’s Room, forever.

## Endless difficulty

The climb never stops scaling:

- **Speed is uncapped.** Everything runs +16% faster per 500-depth tier up to depth 3000 (+80%), then keeps growing at a gentler +4% per tier — forever. A "the tide quickens" hint marks each step-up.
- **Gaps widen** with depth (capped) and shelves **narrow slowly**, reaching their minimum width only around depth 580 so the early-mid climb stays comfortable.
- Drifting, bobbing, spring, and cracked shelves all become more common as you descend.

## Under the hood

Everything lives in `index.html`:

| Piece | How it works |
| --- | --- |
| Rendering | `<canvas>` 2D, `requestAnimationFrame` loop with delta-time physics (clamped to 35 ms) — damping is time-scaled so the feel is identical at 60/144/240 Hz |
| Physics | Gravity + terminal velocity for falls; fixed impulse on landing; spring-like steering toward the pointer |
| Level gen | Procedural platforms with capped vertical gaps (66–92 px baseline) and horizontal offsets bounded by your bounce reach |
| Music | Generative ambient engine — each of the seven themes brings its own tempo, waveform, volume, and four-chord progression; arpeggios run through a feedback delay for space. All synthesized, nothing recorded |
| SFX | Bounce, spring launch, milestone chime, speed-up shimmer, theme shift, power-up grabs, shield block/shatter, wisp shots, start fanfare, loss sweep — all short oscillator envelopes |
| Scene | The room (orb, light beam, rotating sphere, silhouette) is pure CSS — gradients, blur, and keyframe animations on top of the canvas; every theme swaps its palette live |

## Tuning

The game feel lives in one block near the top of the `<script>`:

```js
const G = 1300;   // gravity
const B = -700;  // bounce impulse
const SB = -1150;// spring-shelf launch (much higher)
const BSPD = 230;// base wisp bolt speed (scales to 2x with depth)
const A = 3200;  // steering acceleration
const MV = 420;  // max horizontal speed
const PW = 130;  // platform width
const GAP_MIN = 66, GAP_MAX = 92; // vertical gap range
```

Nudge `B` up for a floatier game, `GAP_*` down for an easier climb. Enemy prevalence and power-up odds are the `.25` / `.12` probabilities in `maybeEnemy()` / `maybePowerup()`.

## Notes

- Works in full-screen — the scene and canvas fill the viewport and adapt on resize.
- Audio starts on your first interaction (browser autoplay rules), so hit **Begin** and everything wakes up.
- Works offline once the file is on disk.
