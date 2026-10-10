# The Keeper’s Room

A tiny endless bouncer — one HTML file, zero dependencies.

Bounce up an infinite tower of glowing platforms and see how deep the tide takes you. Every sound is synthesized live in your browser with the Web Audio API; there are no assets to load.

![screenshot](docs/screenshot.png) <!-- optional: drop a screenshot at docs/screenshot.png -->

## Play it

No build step. Just open [`Bounce.html`](./Bounce.html) in any modern browser (desktop or mobile).

Or, if you want a local URL:

```sh
npx serve .
# → http://localhost:3000/Bounce.html
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

## Under the hood

Everything lives in `Bounce.html`:

| Piece | How it works |
| --- | --- |
| Rendering | `<canvas>` 2D, `requestAnimationFrame` loop with delta-time physics (clamped to 35 ms) |
| Physics | Gravity + terminal velocity for falls; fixed impulse on landing; spring-like steering toward the pointer |
| Level gen | Procedural platforms with capped vertical gaps (66–92 px) and horizontal offsets bounded by your bounce reach |
| Music | Generative ambient loop — Am · F · C · G pads at 74 BPM, triangle-wave arpeggios, and a feedback delay for space. All synthesized, nothing recorded |
| SFX | Bounce, milestone chime, start fanfare, and the loss sweep are all short oscillator envelopes |
| Scene | The moonlit room (orb, light beam, rotating sphere, silhouette) is pure CSS — gradients, blur, and keyframe animations on top of the canvas |

## Tuning

The game feel lives in one block near the top of the `<script>`:

```js
const G = 1300;   // gravity
const B = -700;  // bounce impulse
const A = 3200;  // steering acceleration
const MV = 420;  // max horizontal speed
const PW = 130;  // platform width
const GAP_MIN = 66, GAP_MAX = 92; // vertical gap range
```

Nudge `B` up for a floatier game, `GAP_*` down for an easier climb.

## Notes

- Best in full-screen — the scene and canvas fill the viewport and adapt on resize.
- Audio starts on your first interaction (browser autoplay rules), so hit **Begin** and everything wakes up.
- Works offline once the file is on disk.
