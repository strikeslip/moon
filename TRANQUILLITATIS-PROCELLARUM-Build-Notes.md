# TRANQUILLITATIS · PROCELLARUM: build notes, decisions and data audit

**Deliverable:** `index.html`, a single file of about 2.65 MB with zero dependencies. The six raw MiniSEED day-files are bundled as base64 and decoded in the browser. Host it at `seis.monster/moon/`.
**Date:** 20 November 2026

---

## 1. Data audit (from the six uploaded files)

All six are **Steim-2, big-endian, 4096-byte records** at a nominal 6.625 sps, and each is a single continuous trace. The JavaScript decoder's output matches a pure-Python Steim decoder **sample for sample** on all six files (counts, sums and a positional hash).

| # | File | Coverage (UTC) | Gaps (−1) | Findings |
|---|---|---|---|---|
| I | `xa.s11.00.mhz.1969.202.0` | 07:03:35 → 24:00 | 17.8% | The record opens **after** the EVA, so there are no footfalls. About 4 h of heavy disturbance and saturation follow (crew aboard *Eagle*). **The LM liftoff (17:54) is inside a 17:05:31–18:08:35 telemetry gap.** Re-centring steps at about 18:20, 20:12 and 21:14. |
| II | `xa.s11.00.mhz.1969.207.0` | full day | 44.2% | **10-hour gap 08:29–18:40.** One emergent event at about 01:05; short local events around 19:35. |
| III | `xa.s11.00.mhz.1969.214.0` | full day | 3.6% | Short local events (00:36, 03:30, 12:14) and an emergent event at about 10:30. Instrument excursions at 14:20 and 20:26. Fragmented telemetry after 23:00. |
| IV | `xa.s12.00.mhz.1969.324.0` | full day | 5.8% | **Intrepid impact clearly present:** onset about 22:17:30, peak about 22:22, ringing to about 23:10. The noisy 03:55–07:44 stretch matches EVA-2. LM liftoff (about 14:25) sits inside a gapped, disturbed section. No clipping at the impact. |
| V | `xa.s12.00.mhz.1973.072.0` | 00:00 → 14:21 (partial) | 0.7% | **Shallow moonquake is inside the window:** first arrivals about 08:01:30, peak 08:05–08:15, coda to about 11:00. This is the cleanest file (no saturation). |
| VI | `xa.s12.01.mhz.1975.066.0` | full day | 1.0% | **Flat mode.** No moonquake. Dominated by Earth-commanded pulse trains (09:11–10:20, about 13:20) and an excursion at about 22:50. |

Five of the six files contain many **full-range instrument excursions**: saturation slabs at 0 or 1023, re-levelling steps, and calibration-style pendulum ringing. They are masked out of the main voice and routed to the telemetry bus, not deleted.

**Not in these files:** the ATT timing trace, so clock drift is not used; and SHZ (the short-period channel), so all high-frequency brilliance is synthesis.

---

## 2. Section 13 decisions as implemented

| # | Decision | What was built |
|---|---|---|
| 1 | File 6 | **Kept 1975.066 (flat mode)**, reframed honestly as *VI · EARTH CALLING*. The 1976.066 file could not be fetched (PDS is blocked from the build environment). |
| 2 | SHZ | **MHZ only.** Thermal crystals are glassy pings on high partials of the instrument fundamental, triggered by impulsiveness and temperature drift, and labelled as interpretation. |
| 3 | Frame | **Endless six-movement web instrument + Found Telemetry layer**, plus a **RENDER WAV** button (offline render of the current movement from the same engine) for the fixed version. |
| 4 | Pitch clock | **960× default** (2.2 s pendulum ≈ 436 Hz ≈ A4). Selectable 120/240/480/960×, so the resonance always lands near an A. A separate structure clock time-warps from 24× to 6000×. |
| 5 | Artefacts | **Gaps** are interpolated and kept as a mask; long gaps (> 60 s) collapse to about 1.2 s with a "loss of signal" hiss. **Spikes:** 5-point median repair. **Saturation and steps** are masked (±90 s) and go to the telemetry bus. The **DC drift** becomes the "lunar thermometer", which drives coda-cloud brightness. |
| 6 | Mode | **Character kept.** Peaked mode uses a pendulum-bell resonator bank (f0 = P/2.2). Flat mode uses a free-bar bank (f0 = P/15), folded up by octaves. Gain is normalised per file. |
| 7 | Scope | **ARC** (full day, time-warped to 4/6/10/16 min), **WINDOWS** (curated event windows at 40×) and **HOLD** (repeat the current movement). |
| 8 | Visual | **24-hour dial** (orange = energy per minute; brown = artefact; empty = gap; inner line = thermometer; outer ticks = bookmarks; click to seek). **Greyscale Moon** with S11 and S12, the Intrepid impact marker, and a diffusive glow (not wavefronts). **Telemetry strip** (±45 s, artefacts in brown, gaps as −1, nominal block grid). Orange canon palette. |
| 9 | Title | ***TRANQUILLITATIS · PROCELLARUM*: Apollo Lunar Musification · XA_1969 · a moonwork** |
| 10 | FSS framing | Included in the README modal (CP-12 ended July 2026; Apollo is still the Moon's only long-term seismic record). |

---

## 3. Engine (AudioWorklet)

The waveform is both sound source and control signal. There are seven voices plus space:

- **True voice:** granular audification at the pitch clock (60 ms Hann grains, 4× overlap, Hermite interpolation).
- **Coda cloud:** grains scattered over ±20–80 s of data, 22% an octave down, low-passed by the thermometer.
- **Bell / bar:** 6–8 resonators tuned to the instrument physics, excited by envelope onsets.
- **Seismic FM:** velocity drives the index; ratio 1.5.
- **Sub bed:** the pendulum fundamental divided by 8 (54.5 Hz at 960×).
- **Found telemetry:** the artefact bus played as a nearest-neighbour 10-bit staircase. It adds a 54.34 s block tick when the structure clock is slow (below 160×), clicks at gap edges, and a hiss where long gaps are skipped.
- **Thermal crystals:** glassy high-partial pings.
- **Light delay:** an "Earth" copy of the true voice delayed by 1.28 s ÷ structure rate, in the right channel.
- **Reverb and limiter:** an 8-line FDN ("scatter") with T60 of 4–13 s, then a DC block and a tanh limiter.

All randomness comes from seeded mulberry32 (SEISMETA.makeRng / xmur3 on the main thread), and each movement reseeds at its start, so playback is deterministic.

**House rules respected:** single file; ES5 main thread (ES6 only inside the worklet script); no ObsPy; play-on-press, no autostart; WebKit start (context is created and resumed inside the gesture, plus a silent buffer and `navigator.audioSession = 'playback'`). The worklet loads from a blob URL and falls back to a data URL, so the page also works when opened from `file://`.

**MiniSEED parser:** I wrote a new compact decoder because your house parser spine wasn't available here. It handles SEED 2.4 headers, blockette 1000, Steim-1/2 and INT16/32 in either byte order. If you want to keep the sacred spine, swap it in at `MSEED.parse`. Its output contract is `{start (seconds of day), loc, sta, data: Int32Array}`.

---

## 4. Verification done

- **Decoder parity:** JavaScript vs Python on all six files, exact match.
- **Headless Chrome, realtime:** loads with no console errors; playback advances; movement, form and pitch switching, keyboard bookmarks and the README all work, on a desktop and a 390 px mobile layout.
- **Offline renders of all six movements** (4-min ARC, 960×): no NaN, peaks ≤ 0.82, median RMS between −25 and −32 dB. The movement V quake has a crest factor of about 12 dB, so it isn't over-limited.
- **Spectrograms** show the Intrepid swell and coda, the 1973 quake onset with its long decay, and the VI pulse trains where expected.
- **Not tested:** real Safari or iOS. Check on your MacBook and iPhone as with ELYSIUM v22.

---

## 5. Open items

1. **File VI swap:** to switch to `xa.s12.01.mhz.1976.066.0.mseed`, replace the base64 block for file 6 and update `MOV[5]` (date, marks, windows, note). The bundle builder is a 30-line script, available on request.
2. **Command log:** check the VI pulse trains and the I/IV re-centring steps against the `cerinunn/pdart` command log, then relabel "calibration / re-levelling" precisely.
3. **Nakamura catalog:** check the "local, unclassified" events in I–III and the 1973 event's catalog entry, then update the bookmark labels.
4. **Optional:** add ATT traces to drive true clock-drift flutter in the telemetry layer.
