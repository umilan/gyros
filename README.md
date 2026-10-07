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

**Key mapping**: tap the text in the middle of the strip to bind keys from a
keyboard or Bluetooth remote to the setting it shows. The first key you press
becomes its `+`, the second its `−`; tap the middle again to finish with only
a `+` key. `Esc` while it is listening clears that setting's keys. Mapped keys
work from anywhere, take priority over the built-in shortcuts, and survive
RESET TO DEFAULTS.

**Keyboard** (useful on a desktop, harmless on a phone): arrows or WASD. While
viewing, `↑` opens the strip, `↓` opens the list, `←/→` change eye distance.
`Esc` closes whatever is open. Mouse wheel and trackpad pinch zoom.

**Gamepad** (Bluetooth or USB, standard layout): D-pad or left stick = arrows,
`A` = activate, `B` = Esc, `X` = play / pause, `Y` = swap strip and list,
`LB`/`RB` = `[` / `]`, hold a trigger for Shift, `START` = load dialog,
`BACK` or a stick click = RECENTER. Press any button once so the browser
reports the pad.

## Settings

Everything is shared across content types and saved to `localStorage` under
`gyrosSettings`. The content type itself is not restored — a picked file cannot
be — so it always boots to the demo.

- **GENERAL** — VIEW (PLAIN collapses to a single full-width view and one menu
  pane; SBS is stereo; HOLO is stereo with a mirrored UI for mirror-viewed
  rigs; HOLO2 is HOLO for a phone mounted tilted above the eyes and seen in
  a reflector; HOLO3 is HOLO2 for a see-through reflector, see below),
  AUTO TILT / PHONE TILT / PHONE ROLL (how the phone sits relative to your
  head, kept per view: with AUTO TILT on they are measured each time you
  RECENTER, so hold your head level when you do; step either angle by hand
  to switch that off and fine-tune. On in HOLO2 and HOLO3, off elsewhere).
- **OPTICS** — SBS EYE DIST, EYE DISTANCE, PERSPECTIVE (camera field of
  view), BRIGHTNESS, MENU TIMEOUT.
- **CONTENT** — demo shape, auto-rotate, SBS source.
- **POSITIONING** — POS X/Y, eye distance.
- **ZOOM** — zoom, scale X/Y, keystone top/bottom (TRPZD).
- **FLIP** — flip X/Y.
- **GYRO** — per-axis multiply, freeze and invert, plus RECENTER, which makes
  wherever you are looking the new forward. TRACKING picks the algorithm;
  V2.8 (6DOF) adds motion prediction, adaptive smoothing, a yaw-only
  RECENTER that keeps the horizon level, and a neck model that fakes head
  translation (6DOF DEPTH sets how strong).

### HOLO3: see-through visors

Through a clear visor you see the room behind the picture, so the picture
has to turn exactly as far and as soon as your head does or it visibly slides
over the room. HOLO3 tracks like HOLO2 and adds four rows under VIEW:

- **PREDICTION** — how many milliseconds ahead the head pose is extrapolated.
  Turn your head steadily: if the picture trails behind the room, raise it;
  if it overshoots when you stop, lower it.
- **SMOOTHING** — how much sensor noise the tracking allows for, as a percent
  of what HOLO2 uses. Lower follows your head more closely; raise it if the
  picture trembles while you hold still.
- **FIELD OF VIEW** — the cameras' vertical field of view in degrees. It is
  right when it equals the angle the picture really covers in the visor.
- **MEASURE FOV** — finds FIELD OF VIEW for you. Press it, close one eye and
  put the cross on something a few metres away; press again; turn your head
  until the side line sits on the same thing; press a third time. Pressing
  the third time without having turned cancels. Measure again after changing
  ZOOM, SCALE or X MULTIPLY.

The three values are shared settings that only HOLO3 reads.

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
