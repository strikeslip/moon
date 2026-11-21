# ISM-02 · APOLLO MOON MUSIFICATION (Tranquillitatis · Procellarum)

**Build notes. 21 November 2026.**

**Deliverable:** `moon/index.html`, a single file of about 0.99 MB with no libraries and no network calls. Deploy it to `seis.monster/moon/`.

---

## 1. How ELYSIUM MARS v22 was reverse-engineered

The Moon page keeps the v22 file layout, six script blocks in the same order:

1. `i18n`
2. bundle (base64 MiniSEED)
3. `mseed-parser`
4. engine
5. terrain
6. vision

What happened to each part of the Mars build:

| Mars v22 part | Moon (ISM-02) |
|---|---|
| **MiniSEED parser** | **Copied byte for byte.** The build verifies this with `cmp`. It decodes Apollo's Steim-2 big-endian records unchanged. |
| **Bundle:** nine InSight files | **Six Apollo windows**, cut as whole, untouched 4096-byte MiniSEED records (about 140 KB). |
| **Shared engine code:** grains, wavetable `note()`, phases, shared-scale features, accumulator, sets, transport, V22 WebKit/iOS start | **Kept.** |
| 880× standard (1 Hz → A5) | **960×** standard, so the 2.2 s Apollo pendulum sounds at 436 Hz ≈ A4. This follows the ISM rule that a physical reference in each body's data lands on A. |
| G major, 108 BPM | **A, 99.375 BPM.** The Apollo sampler is the tempo: one 16th = one MHZ sample (1/6.625 s), one beat = one telemetry frame. |
| Loops 5 / 7 / 4 from sol numbers, HOME every 140 bars | **Loops 3 / 11 / 12.** The 11-eighth cell comes from Apollo 11, the 12-eighth bass from Apollo 12. HOME comes every **132 bars = 11 × 12**. |
| Sol clock, 1 HOME = 1 Mars hour | **Lunar clock**, 1 HOME = 1 lunar hour. Thermal crackle is sharp at sunrise and broad at sunset; the night is earthshine. |
| Sets every 95 bars: Band, Curved Air, Dub, Mariner, Broken, Drone | **Every 90 bars** (one 90-frame telemetry block): Rainbow, Curved Air, Poppy Nogood, Surveyor, Broken, Drone. **The station alternates** each set, so all 12 station × arrangement pairs play in about 43 min. |
| Kick, rim, hats | **Dum, tek, tambourine** (Riley's hand drum and jingles) |
| Dotted-8th delay | **Earth echo:** 2 beats = 1.21 s, inside the 1.2–1.4 s Earth–Moon light-time |
| Three marsquakes, each with a role | **Two line-ups of three, each with its own melody cell.** S11: room 207, singer 214, rhythm 202. S12: room 1975.066, singer 324 (Intrepid impact), rhythm 1973.072 (shallow moonquake). |
| Vision scenes: Rise, Plains, Caldera, Orbit | **Rise** (Copernicus orbit, with Earth in the sky), **Plains** (Tranquillitatis ↔ Procellarum), **Crater** (Copernicus, melody rings), **Orbit** (0.5° globe with a terminator from the lunar clock) |
| Mint and rust palette; Mariner 9 dust storm | **Black, white and blue**; **lunar sunrise** (summits are lit first) |
| Languages EN / 中文 / RU / FR, Readme, Controls, play button with 火 | Same four languages and controls; the play button carries **月**. A link reads **MARS ↗** to ISM-01. |

---

## 2. Changes made only for the Moon

- **Gaps and spikes.** The archive fills gaps with −1. These are interpolated, then residual spikes are repaired against a 5-point median.
- **Lunar AGC on the play buffers.** Apollo's 10-bit background between events is only a few digital units deep. Without correction, the wavetable grains would be about 30 dB quieter than on Mars. Features and phases still read the un-gained data, so the band's dynamics stay real.
  - Measured median level in headless Chrome: Mars 0.092, Moon S11 0.057, Moon S12 0.045 at the window start.
  - Moon S12 rises to 0.31 as the scan reaches the 1973 quake and the Intrepid impact.
- **Local lunar time.** Computed from the Moon's mean elongation. As a check, it puts the Sun about 10° up at the Apollo 11 landing (the real figure was about 10.8°) and about 3.6° up at the Apollo 12 landing (the real figure was about 5°).

---

## 3. Lunar topography

| Item | Detail |
|---|---|
| Model | **MoonTopo2600p** (M. A. Wieczorek 2015). This is the lunar sibling of the MarsTopo719 model ELYSIUM uses, so the series keeps one source family. |
| Grid used | The **10′ grid published by Fatiando a Terra** (`moon-topography-10arcmin` v1, CC BY 4.0). Downloaded from GitHub; the MD5 matches the published checksum. |
| Regional tile | 1/6° (about 5 km), covering 15°N–15°S and 40°W–40°E |
| Globe | 0.5° |
| Copernicus in the grid | Rim about +0.3 km, floor about −3.2 km. The real crater is about 3.8 km deep, so the grid smooths it slightly. |
| Station heights | S11 about −1.7 km, S12 about −1.2 km |
| Blue | Low ground below −0.9 km: the maria and the deepest crater floors |

---

## 4. Verified in headless Chrome

- All six files are perceived.
- Both line-ups are built, and each derives its own 11-note melody cell.
- The band plays, and the phases respond to the data:
  - the rhythm (1973 quake) goes FLOOR → DRIFT → PEAK;
  - the singer (Intrepid) goes to PEAK and its melody cell grows.
- All four scenes work, in all three modes (Pixel, ASCII, Hybrid).
- Sunrise, noon and night lighting all render.
- All four languages work.
- The mobile layout works.
- There are no console errors.

**Not tested:** real Safari or iPhone. Check these as with ELYSIUM v22.

---

## 5. Notes

- **File 6 is still the 1975 flat-mode day,** used as the S12 room. Dropping in the 1976 file (or any XA day-file) through Controls adds it to the band; the engine plays its strongest 75 minutes.
- **No Riley melody is quoted.** The influence is structural: modal organ cells, odd cycle lengths over a steady pulse, hand drum and tambourine, and the time-lag accumulator.

---

## 6. Update: launch sequence and new title (21 November 2026)

**Title:** "APOLLO **MOON** MUSIFICATION". MOON is set in the S11 blue (`#4a8dff`) in the header, the page title, the README in all four languages, and the engine panel.

**LAUNCH SEQUENCE intro (16 bars ≈ 38.6 s):** on by default; it can be switched off in Controls.
- A countdown runs from T−16 to T−1, with a tick on every bar.
- The sequencer starts sparse and dark: quarter notes, then 8ths, then 16ths, with the filter opening.
- String pads swell. The octave-pulse bass enters at T−8. An ignition noise swell rises through the last bar.
- At bar 17 comes liftoff: a falling rumble and a sub drop, then set 1 begins.
- Visuals: the Rise camera climbs during the countdown, then cuts to Orbit after liftoff.

**LAUNCH set:** seventh in the rotation.
- Shift+7 or the L key plays it now.
- All 14 station × arrangement pairings play in about 50 minutes.
- A new **LAUNCH SYNTH** fader controls its level.

**Voices:**
- These are ISM's first oscillator voices: a sawtooth plus a detuned square or saw, through resonant low-pass filters with envelopes.
- They run through the Earth echo and the Riley accumulator.
- The data supply the touch:
  - each station's melody cell gives the sequencer notes;
  - the room's energy opens the filter;
  - the rhythm's zero crossings place the accents;
  - every 16th step is one Apollo sample.

**Style only:** the 1980s analog-sequencer space-synth idiom. Nothing from Tim Stebbing's "Launch Sequence" (1985) is transcribed or embedded: no melody, bassline, arrangement, tempo (it runs at about 136 BPM; ISM-02 stays at 99.375) or key (it is in D; ISM-02 stays in A).

**Measured in headless Chrome (median RMS):**

| Moment | Level |
|---|---|
| Countdown | 0.03 → 0.10 |
| Liftoff | 0.16 |
| LAUNCH set | about 0.09 |
| Band after the intro: S11 / S12 | 0.088 / 0.119 |
| Mars v22, for reference | 0.099 |

No console errors.

## 7. PHASE layer + repeatable piece (after SEIST3RRY)

**Repeatable.** One seed, `APOLLO MOON MUSIFICATION · ISM-02`, drives a xmur3 → mulberry32 RNG (`rnd()`); all 16 former `Math.random()` calls in the engine use it. The noise buffer has its own fixed seed. `plan()` now runs on the step clock (every 2 steps) instead of the screen timer, so decisions follow musical time. A fresh Begin calls `reseed()` + `phaseReset()`. Tested: two headless runs gave 509/509 identical note/phase/set events over the first 40 bars. (Vision sparkle positions still use `Math.random()`; they're visual only.)

**PHASE fader** (default 55) and a `phase` amount per set: Rainbow .5 · Curved Air .4 · Poppy Nogood 1.2 · Surveyor .8 · Broken .35 · Drone 1.3 · Launch .3 · countdown 0. Bus `g.phase` → low-pass 5.2 kHz → sum + Riley accumulator.

- **Phasing voices:** five players always count; 3–5 are heard (entropy). They share one 6–8 note cell (length from the singer event's strength). Tempo `mult = 1 + vn·(0.0015 + entropy·0.004)`. This is deliberately slower than SEIST3RRY's 0.008/0.025, so the farthest voice laps the cell a few times per HOME cycle instead of every few bars. Voice 0 stays on the Apollo grid. At HOME (every 132 bars) all voices snap back onto the grid and emit `phasesync`.
- **Timbre:** seismic grains via `note()` from the singer · room · rhythm waveforms in rotation, an octave up every 4th note, fitted to the chord, level normalised by √(3/nV).
- **Territories:** the station's own home territory (its cell's 3 most-used degrees), then six fixed 3-degree sets of A pentatonic. A shift needs a chord-block line plus either a data surge (shared-scale Mx after ≥4 bars) or the dwell running out (6–15 bars). Each station change resets to its home territory.
- **Melodic memory:** avoids the last note (in a 3-degree territory) and leaps for contrast 60% of the time.
- **Entropy (lunar stand-in):** a new per-event feature, `entS` = smoothed 0.6·derU + 0.4·zcrZn, normalised to the 95th percentile. It reads rhythm 60% and singer 40%. The raw zcrZn was almost always 0 on S11.
- **Dervish:** sines at 110 Hz × [1, 3/2, 2, 3, 4], from 1 up to 5 partials (entropy × event strength). Detune ±1.2% × coda share, so they beat slowly; panning rotates. Full strength in Drone and Surveyor, otherwise scaled by lunar night. They stop on Stop.
- **Growth:** the layer rises in the lunar night and when the band's phases sit in CODA or FLOOR.
- **Vision:** `phase` events put small glyph rings around the active station, one radius per voice, drifting apart (Plains and Crater). `phasesync` brings a white ring and a flash.
- **Text:** PHASE fader label and hint, plus README sections in EN / ZH / RU / FR. The header comment is updated.

Levels (headless, median RMS): Mars v22 0.09 · Moon S11 0.088 · S12 0.107. With PHASE on or off, levels stayed in the same range. The occasional 0.7–0.99 peaks happen with PHASE at 0 too and come from the shared compressor chain. No console errors.
