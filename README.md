# 🌅 Sun-Side — Know Which Side of the Bus to Sit On

**A single-file, no-backend web app that tells you which side of a vehicle to sit on to avoid sun glare — for any route, anywhere in the world.**

Type a starting point, a destination, and your departure time. Sun-Side geocodes both places, pulls the real road route, breaks it into direction-based segments, tracks exactly where the sun will be at every point in your journey, and tells you: **sit left, sit right, or it doesn't matter** — segment by segment, with an overall recommendation.

No sign-up. No API keys. No backend server. It's one HTML file that runs entirely in your browser.

---

## ✨ Why this exists

If you've ever taken a long bus, train, or car ride facing straight into the sun for an hour because you picked the wrong seat — this is for that. Routes curve, the sun moves, and "just sit on the shady side" isn't a single answer for a 3-hour trip. Sun-Side does the math so you don't have to guess.

---

## 🖥️ Try it

Since this is a single static HTML file, you can run it in two ways:

1. **Open it locally** — download `sun-side.html` and double-click it, or
2. **Host it for free** — enable **GitHub Pages** on this repo (Settings → Pages → deploy from `main`) and share the link.

No build step, no `npm install`, no config.

---

## 🧠 How it works

```
From / To / Departure time
        │
        ▼
1. Geocode both addresses           →  OpenStreetMap Nominatim
        │
        ▼
2. Fetch the real road route        →  OSRM public routing server
        │
        ▼
3. Flatten the route into a
   timed sequence of points          (each point gets a running clock)
        │
        ▼
4. Split the route into segments     (new segment on a sharp turn,
                                       or after 20 km / 20 min straight)
        │
        ▼
5. Compute compass bearing per segment
        │
        ▼
6. Compute sun azimuth + elevation
   at each segment's time & place    →  hand-written NOAA-style solar math
        │
        ▼
7. Decide: sun hits left / right /
   neither window, per segment
        │
        ▼
8. Merge consecutive same-verdict
   segments into readable blocks
        │
        ▼
Output: overall verdict + color-coded timeline + color-coded map
```

---

## 🛠️ Tech stack

| Piece | Tool | Why |
|---|---|---|
| Geocoding | [OpenStreetMap Nominatim](https://nominatim.org/) | Free, keyless, global address search |
| Routing | [OSRM](http://project-osrm.org/) public demo server | Free, keyless, real road geometry + step timing |
| Sun position | Hand-written JavaScript | Simplified NOAA-style solar algorithm — pure math, no API |
| Map | [Leaflet.js](https://leafletjs.com/) + OSM tiles | Lightweight, keyless, easy route coloring |
| Everything else | Vanilla HTML / CSS / JS | Single-file, dependency-free, works anywhere |

**No backend. No database. No API keys.**

---

## 📋 What it does, step by step

1. **Address autocomplete** — debounced live suggestions as you type your From/To.
2. **Real routing** — an actual road path is fetched, not a straight line between two points.
3. **Smart segmentation** — a new segment starts when the road bends more than ~18°, or after 20 km / 20 minutes of straight travel (so a long highway doesn't hide a big sun shift).
4. **Per-segment sun math** — azimuth and elevation are calculated at each segment's actual midpoint *and* midpoint in time.
5. **Verdict logic** — compares the sun's direction to the direction of travel:
   - Sun roughly ahead or behind → doesn't matter
   - Sun ~40°–140° to one side → glare on that side, recommend the other
   - Sun above ~58° elevation → nearly overhead, minimal glare regardless of angle
   - Below ~20° elevation → flagged as "strong" glare risk (sunrise/sunset conditions)
6. **Merge & summarize** — consecutive same-verdict segments collapse into readable blocks like *"sit left for the next 40 minutes."*
7. **Visual output** — a color-coded timeline strip and a color-coded route on the map.

---

## ⚠️ Known limitations (by design, for V1)

| Limitation | Notes |
|---|---|
| Driving profile used for buses/trains too | No public transit-speed routing profile exists; timing may be slightly off for trains |
| No live traffic | OSRM assumes free-flowing roads; output is labeled as an estimate |
| Single time zone assumed for the whole trip | Uses your browser's local time zone throughout — trips crossing time zones can drift in accuracy |
| Segment midpoint used for sun position | A small smoothing effect, not significant at this granularity |
| Thresholds (18° bearing, 20 km/min caps, 58° elevation cutoff) | Reasonable rules of thumb, not scientifically calibrated |
| No window/seat geometry modeling | Answers *which side*, not *how bad* — doesn't account for aisle vs. window seat or vehicle type |

---

## 🗺️ Roadmap / ideas for V2

- [ ] Timezone-boundary-aware departure time (fixes the biggest accuracy risk)
- [ ] Live "nudge departure time ±15/30 min" control without a full re-route
- [ ] Continuous glare-severity score instead of a hard elevation cutoff
- [ ] Retry/backoff + friendlier errors for Nominatim/OSRM rate limits
- [ ] Basic in-memory caching for repeated queries
- [ ] Glare severity shown directly on the map (not just the timeline)
- [ ] Accessibility pass (screen-reader flow, live-region status announcements)
- [ ] Real GTFS integration for specific transit lines
- [ ] Live traffic-adjusted timing
- [ ] Multi-modal trips (walk + bus + train combined)

---

## 🎨 Design notes

- **Concept:** a torn "boarding pass" ticket stub for the hero result — verdict on one side, trip stats on the other.
- **Palette:** deep dusk-indigo background, warm gold for "sit right," teal for "sit left" (equal visual weight, no color implies "better"), coral reserved only for errors.
- **Type:** serif display font for the verdict, system sans for body copy, monospace for all timings/durations — all system fonts, so nothing breaks if a CDN is blocked.
- **Signature visuals:** a width-proportional glare timeline strip, and a color-coded route polyline on the map.

---

## 📁 Project structure

```
.
└── sun-side.html   # the entire app — HTML, CSS, and JS in one file
```

---

## 🤝 Contributing

This is a solo/hobby-scale V1. Issues and PRs for bug fixes, accuracy improvements, or any of the roadmap items above are welcome.

## 📄 License

MIT — do whatever you'd like with it.
