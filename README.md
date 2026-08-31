# Stargazer

Point a phone at the sky (or a window, or the floor) and get back a labelled view of the
stars, planets and Moon that are actually there — driven by geolocation + device orientation,
computed locally, no backend. Built for a vibe-coding competition.

Target device: **Android (Samsung A35), Chrome.** Must be served over HTTPS for sensors to work.

## Feasibility plan — burn down the risky unknowns, cheapest first

| Phase | Question it answers | Status | Go / no-go |
|---|---|---|---|
| **1. Sensor spike** | Can this phone give a usable compass heading? | ✅ **pass** | `deviceorientationabsolute` (`absolute:true`), 60 Hz while moving, heading tracks a compass app, GPS fix ±4 m |
| **2. Astronomy pipeline** | Does lat/lon + time → correct alt/az? | ✅ **pass** | page's Rigel = independent recompute to 2 dp; Moon confirmed against the real sky |
| **3. Sky diorama (three.js)** | Is it a real planetarium, and does it look good? | 🔨 **built, needs phone test** | sky matches reality for Brisbane; drag-look / phone-orientation / camera pass-through need on-device check |
| **Decision point** | Has it got legs? | — | kill only if P3's phone-orientation mode is unusable **and** it underwhelms |
| **4. Capture + share** | The thing you show the judges | next | labelled PNG / mini-diorama stamped with location + time, shareable by link |

## Phase 1 — `spike-sensors/`

Zero-build static page. Open it on the phone (see URL below), tap **Start sensors**, and read:

- **Compass heading** — tilt-compensated, screen-orientation corrected. Compare to a real compass app.
- **Update rate (Hz)** and **jitter (p-p over 1 s)** — the wobble while holding still.
- **Drift** — tap *Mark heading*, wait, see how far it wanders.
- **Event source** — `deviceorientationabsolute` (Earth frame, good) vs relative `deviceorientation` (heading unreliable).
- Tilt bubble + compass rose for a live feel.

Test matrix: outdoors away from metal · indoors by a window · next to a laptop · phone upright aimed at the
horizon then tipped up to the sky. Then **Copy diagnostics** and paste the JSON back.

## Phase 3 — `spike-sky/`

Zero-build three.js planetarium (ES module + importmap, all deps vendored):

- `stars.json` — HYG v41 trimmed to mag ≤ 6 (5,070 stars, 347 named), coloured by B–V, sized by magnitude.
- `constellations.lines.json` — d3-celestial western constellation figures.
- of-date star coords computed once at load via `astronomy-engine` (precession/nutation/aberration);
  a fast local-sidereal formula re-places them on every time change (~4 ms vs ~200 ms).
- planets / Moon / Sun via `astronomy-engine` each frame-set; twilight sky colour + star fade driven by Sun altitude.
- **look:** drag (default) · **phone** (device-orientation quaternion + a heading-nudge slider to correct the magnetometer) · **camera** pass-through toggle for real AR.
- location + freeze/scrub time controls; HUD shows view az/alt and Moon/Saturn az/alt for a sanity check.

Open on the phone, tap **Use location**, then **look: phone** and **camera: on**, point at the sky.

## Local dev

Static — `npx serve .` then open the spike you want. No build step; deps are in each spike's `vendor/`.

## Deploy

GitHub Pages from `main` / root. Live URL: **https://easybeinggreen.github.io/stargazer/**
(sensor spike at `/spike-sensors/`).
