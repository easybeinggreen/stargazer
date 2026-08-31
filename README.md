# Stargazer

Point a phone at the sky (or a window, or the floor) and get back a labelled view of the
stars, planets and Moon that are actually there — driven by geolocation + device orientation,
computed locally, no backend. Built for a vibe-coding competition.

Target device: **Android (Samsung A35), Chrome.** Must be served over HTTPS for sensors to work.

## Feasibility plan — burn down the risky unknowns, cheapest first

| Phase | Question it answers | Effort | Go / no-go |
|---|---|---|---|
| **1. Sensor spike** | Can this phone give a usable compass heading? | ½ day | heading within ~15° of a real compass outdoors after a figure-8; standing-still jitter < ~3°; ≥ 10 Hz; no runaway drift |
| **2. Astronomy pipeline** | Does lat/lon + time → correct alt/az? | ½ day | Moon within ~1° of Stellarium Web; bright stars match |
| **3. Static sky diorama (three.js)** | Is the artifact actually compelling? | 1 day | sky shapes match Stellarium **and** it looks good; panorama version registers to the real horizon |
| **Decision point** | Has it got legs? | — | kill only if P1 is unfixably bad **and** P3 underwhelms |
| **4. Live AR** | Can labels track the sky as you pan? (stretch) | 1 day | Moon label stays within ~10° while panning |
| **5. Capture + share** | The thing you show the judges | ½ day | labelled PNG stamped with location + time |

## Phase 1 — `spike-sensors/`

Zero-build static page. Open it on the phone (see URL below), tap **Start sensors**, and read:

- **Compass heading** — tilt-compensated, screen-orientation corrected. Compare to a real compass app.
- **Update rate (Hz)** and **jitter (p-p over 1 s)** — the wobble while holding still.
- **Drift** — tap *Mark heading*, wait, see how far it wanders.
- **Event source** — `deviceorientationabsolute` (Earth frame, good) vs relative `deviceorientation` (heading unreliable).
- Tilt bubble + compass rose for a live feel.

Test matrix: outdoors away from metal · indoors by a window · next to a laptop · phone upright aimed at the
horizon then tipped up to the sky. Then **Copy diagnostics** and paste the JSON back.

## Local dev

Phase 1 is plain HTML — just open `spike-sensors/index.html`, or `npx serve .`. Later phases add Vite + three.js + `astronomy-engine`.

## Deploy

GitHub Pages from `main` / root. Live URL: **https://easybeinggreen.github.io/stargazer/**
(sensor spike at `/spike-sensors/`).
