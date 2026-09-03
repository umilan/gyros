# GYROS

A side-by-side stereo viewer for a phone in a Cardboard-style headset. One
self-contained `index.html` — no build step, no dependencies to install. Three.js
r160 loads from jsdelivr at runtime, so the page needs a network connection the
first time.

## Running it

Open `index.html` over **https** (or `localhost`). Two things break on `file://`
and on plain `http` from another host:

- ES module imports are blocked on `file://`.
- `DeviceOrientationEvent.requestPermission()` — the iOS motion prompt — only
  works on a secure origin.

Any static host works. See the `gh-pages-single-file` skill for the GitHub Pages
route.

## Content

Tap the folder icon, then choose **FROM FILE** or **FROM URL**.

- **Demo** — a rotating wireframe or solid: cube, coloured cube, sphere, torus,
  pyramid, octahedron, icosahedron, crystal, or OFF. The colour cube is the
  useful one for checking lens alignment.
- **3D model** — `.obj` or `.stl`, up to about 5 MB. OBJLoader parses text
  line-by-line in JavaScript, so decimate anything heavier first or the tab
  hangs. Materials are ignored; everything gets one grey standard material.
- **Video / stereo image** — a local file or a URL. A remote source must send
  CORS headers or it will not load. Filenames containing "SBS" switch the source
  to side-by-side automatically; otherwise flip `SBS SOURCE` by hand.

## Controls

**In the headset**, on the viewer surface:

| gesture | does |
|---|---|
| drag | pan (POS X / POS Y) |
| pinch | zoom |
| two fingers up/down | eye distance |
| tap | show / hide the controls |
| double tap | recenter |

**Chrome**: folder = load, the GYROS logo = the full settings list, `?` = help,
gear = the settings strip in the bottom bar.

The **strip** shows one setting at a time — `‹ ›` change the value, `↑ ↓` move
between them, and moving past either end closes it. Holding `‹` or `›`
accelerates. The **full list** is the same settings laid out per eye; the focused
row stays centred and the menu scrolls beneath it.

**Keyboard** (useful on a desktop, harmless on a phone): arrows or WASD. While
viewing, `↑` opens the strip, `↓` opens the list, `←/→` change eye distance.
`Esc` closes whatever is open. Mouse wheel and trackpad pinch zoom.

## Settings

Everything is shared across content types and saved to `localStorage` under
`gyrosSettings`. The content type itself is not restored — a picked file cannot
be — so it always boots to the demo.

- **GENERAL** — 3D SBS (off collapses to a single full-width view and one menu
  pane), PERSPECTIVE (camera field of view).
- **CONTENT** — demo shape, auto-rotate, SBS source.
- **POSITIONING** — POS X/Y, eye distance.
- **ZOOM** — zoom, scale X/Y, keystone top/bottom (TRPZD).
- **FLIP** — flip X/Y.
- **GYRO** — per-axis multiply, freeze and invert, plus RECENTER, which makes
  wherever you are looking the new forward.

POS, zoom and scale move and stretch each eye's camera frustum rather than the
finished picture, so nothing is clipped at the viewport edge and magnified
content stays sharp. Flip and keystone are display distortions and stay on the
blit quad.

## Known limits

- Flat plane only — not true VR180/360 projection.
- No orbit gesture for models; one-finger drag is pan.
- POS has no clamp, so panning far enough moves the content out of view.
- No USDZ / AR Quick Look path.
- **Never tested on real hardware.** Gyro behaviour, the iOS permission tap, the
  gesture feel and the useful range of eye distance and keystone are all
  unverified on a phone in an actual headset.
