# Apollo Lunar Seismic Musification

**Project Context:** Translating six curated Apollo Passive Seismic Experiment (`XA.*.00.MHZ`) continuous MiniSEED datasets into a musical and visual earthwork (or *moonwork*), extending the bespoke synthesis and web-instrument lineage of **Sounds of Seismic (`sos.allshookup.org`)** and **`seis.monster/mars/`**.

---

## 1. Executive Vision: From Terrestrial Tectonics to Lunar Resonance

Earthquake musification pioneered by **Sounds of Seismic (SOS)** moves beyond literal speed-up audification—which tends to yield uniform explosions or percussive thuds—toward expressive electronic instrumentation. Through instruments such as *ShadowZone*, *ANMO Advanced Seismic FM Synth*, *Kamchatka 8.8*, *SeisClaw*, and the planetary evolution at *SEIS.MONSTER (`mars`)*, SOS treats seismic waveforms as living excitation sources for granular clouds, modal resonators, FM/harmonic engines, and 3D browser visualizations.

Applying this methodology to the **Apollo Passive Seismic Experiment (PSE)** introduces a fundamentally different physical medium:

* **Earth** exhibits crisp $P$-wave, $S$-wave, and surface-wave phase separation attenuated over minutes.
* **Mars** (InSight SEIS) introduces atmospheric wind noise, thermal lander glitches, and sparser crustal scattering.
* **The Moon** has a bone-dry, intensely fractured megaregolith with virtually zero water-driven attenuation (extremely high $Q$). Seismic energy scatters thousands of times near the surface, creating **slow, emergent ramp-ups** (often 5–10 minutes to peak) and **hour-long ringing codas**—famously described as the Moon *"ringing like a bell."*

The **"Less is More"** curation of these six vertical mid-period (`MHZ`) 24-hour records provides a complete dramaturgical arc across human exploration, thermal physics, kinetic impact, and deep natural moonquakes.

---

## 2. Conceptual & Curatorial Options

Before locking the DSP architecture, the project should select one (or a hybrid) of three overarching artistic formats:

### Option A: Interactive Planetary Web Instrument (`seis.monster/moon/` Model)
A dedicated, zero-dependency browser instrument directly paralleling `seis.monster/mars/` and SOS's Web Audio synthesizer suite.
* **User Experience:** Listeners select among the six Apollo mission days (represented as selectable lunar epochs/nodes in a minimalist 3D selenographic interface), scrub through the 24-hour recording or jump to curated event markers (e.g., Apollo 11 EVA footfalls, LM liftoff, Apollo 12 LM impact), and manipulate real-time synthesis macros (e.g., *Lunar Ringing*, *Thermal Glitch*, *Granular Spread*, *Time Compression*).
* **Strengths:** Highest accessibility, immediate continuity with `seis.monster/mars/`, natural pairing with WebGL/S3D visualization.

### Option B: Six-Movement Electroacoustic Suite ("A Lunar Day in Six Movements")
A fixed or semi-generative album/performance composition where each of the six MiniSEED files serves as a distinct movement (~4–8 minutes each), emphasizing macro-structure and compositional arc.
* **Movement Arc:**
  1. *First Contact & Liftoff* (`S11 1969.202`) — Human presence, mechanical transients, ascent shock.
  2. *Quietude* (`S11 1969.207`) — Mid-day lunar interior breathing, sparse natural L-events.
  3. *Thermal Noon* (`S11 1969.214`) — High-noon thermal expansion cracking and long sustained resonance.
  4. *The Controlled Bell* (`S12 1969.324`) — Sharp artificial LM impact, 7-minute emergent swell, 55-minute coda.
  5. *Shallow Fracture I* (`S12 1973.072`) — Massive natural shallow moonquake (`YN-SMQ-9`, $M_b > 6.5$).
  6. *Shallow Fracture II* (`S12 1976.066`) — High-frequency natural shallow moonquake (`YN-SMQ-26`, $M_b > 6.1$).
* **Strengths:** Allows deep mastering, spatial mixing, and concert/exhibition presentation alongside visual projections (comparable in polish to *Mars Symphony* or *Moonquake* installations).

### Option C: Autonomous "SeisClaw-Lunar" Broadcast Agent
Adapting SOS's **SeisClaw** autonomous agent architecture (*SeisConduct Loop*) from live USGS feeds to a **lunar-cycle-synchronized** continuous broadcast.
* **Mechanism:** Because Apollo data is historical (1969–1976), an autonomous web agent instead synchronizes playback and synthesis parameters to the **current real-time lunar phase / solar illumination angle** at Tranquility Base (S11) and Oceanus Procellarum (S12). As the real Moon waxes toward lunar noon, the engine favors `1969.214` thermal textures; during full/new tidal stress peaks, it swells into `1973.072` and `1976.066` shallow moonquakes.
* **Strengths:** Turns archival 1969–1976 data into a living, perpetually evolving time-anchored soundscape.

---

## 3. Data Reality & Pre-Processing Strategy (`XA.*.00.MHZ`)

### Technical Profile of the Apollo `MHZ` MiniSEED Files
Unlike modern 20–100 Hz broadband (`BHZ`/`HHZ`) terrestrial streams used in standard SOS instruments, the PDS-archived Apollo continuous `MHZ` files (re-archived by Ceri Nunn et al. at NASA JPL / PDS) carry distinct legacy constraints:
* **Native Sample Rate:** **`6.625 Hz`** (Nyquist frequency $\approx 3.3125\text{ Hz}$), derived from the 1969 ALSEP telemetry clock.
* **Resolution & Dynamic Range:** Originally **10-bit A/D telemetry**, now packaged as 4096-byte MiniSEED records (often `FLOAT64` or integer counts in the PDS/EarthScope SEED archive).
* **File Span:** Each daily file contains up to ~572,400 samples (24 hours), but frequently includes **telemetry dropouts/gaps**, **single-sample digital bit-errors (spikes)**, **daily calibration pulses**, and (for Apollo 11) **spacecraft thermal venting / EVA mechanical disturbances**.

### Pre-Processing Options

#### Strategy 1: Scientific Conditioning (Clean Signal Path)
Recommended as the primary excitation input for tonal, harmonic, and modal synthesis:
1. **Gap Handling & Stitching:** Detect non-contiguous MiniSEED time blocks (`ATT`/`AFR` frame anomalies). Rather than hard zero-padding (which causes loud audio clicks), apply short cosine crossfades into silence or low-level synthetic regolith noise floors, while logging gap timestamps for visual UI indicators.
2. **Digital Spike Removal (De-glitching):** Apply a rolling Median Absolute Deviation (MAD) despiking filter (aligned with Ceri Nunn's PDS deglitching workflow) to catch single-sample telemetry bit-errors before they trigger false synthesizer transients.
3. **DC Offset & Thermal Drift Removal:** Apply a gentle zero-phase high-pass filter (e.g., 0.05–0.1 Hz) to eliminate slow thermal drift across the lunar day without losing the `0.3–1.1 Hz` peaked-mode moonquake band.
4. **Dynamic Normalization:** Use dual-stage envelope tracking—preserving relative macro-dynamics between quiet background and peak moonquakes while keeping low-amplitude L-events and EVA footsteps audible without clipping on `YN-SMQ-9`.

#### Strategy 2: "Found Telemetry" Dual-Stream Separation (Creative Artifact Path)
Inspired by SOS's *Kamchatka* instrument (which features explicit *Found Sound* and *Tape Glitch* parameters):
* Instead of discarding the Apollo 11 glitches, calibration steps, and telemetry spikes, **split the signal into two parallel analysis buses**:
  * **Bus A (Clean Geophysical Waveform):** Despiked, detrended lunar motion driving tonal pads, modal bells, and sub-bass.
  * **Bus B (Artifact & Telemetry Residual):** The *difference* between raw and despiked data (digital spikes, dropouts, S11 LM thermal pops). Bus B becomes a controllable micro-percussion / radio-artifact layer, honoring the 1969 10-bit S-band transmission medium rather than sanitizing it completely.

---

## 4. Musification & DSP Synthesis Design Options

Because `6.625 Hz` lies far below human hearing (`20 Hz – 20 kHz`), **time compression alone is insufficient**: even speeding up 24 hours by `64×` (to ~22.5 minutes) only pushes the Nyquist ceiling to `212 Hz`, yielding dark sub-rumble. Following the SOS / *ShadowZone* philosophy, the waveform must act as both **micro-acoustic source material** and **macro-control voltage (CV)** for multi-layered synthesis.

### Time-Scale Architecture Options
* **Event-Zoomed Windowing (Real-Time Feel):** Compress a 60-minute lunar event (such as the `1969.324` impact) by `8×–16×` into a 4–7 minute musical piece, preserving the eerie slow-motion emergent build-up.
* **24-Hour Macro-Scan with Dynamic Time-Warping:** Compress quiet inter-event hours aggressively (`120×–300×`) and automatically decelerate (`4×–16×`) when RMS energy crosses a moonquake detection threshold—allowing a listener to experience an entire 24-hour MiniSEED file in ~6 minutes without missing detail.

### Four-Layer Hybrid Synthesis Engine (SOS / `SEIS.MONSTER` Lineage)

#### 1. Multi-Cloud Granular Engine (*ShadowZone* Heritage)
The `6.625 Hz` trace is upsampled/interpolated (cubic spline) into an audio-rate wavetable or modulation buffer, feeding three concurrent granular clouds:
* **Lunar Coda Wash (Texture Cloud):** High-density overlapping grains (`20–80 ms`) reading directly across the upsampled seismic waveform, smeared through stereo dispersion to evoke the endless scattering of the megaregolith.
* **Regolith Scatter (Crystal Cloud):** Sparse, short-duration (`5–25 ms`) high-clarity grains triggered stochastically when the waveform's zero-crossing rate or high-frequency derivative spikes (ideal for `1969.214` thermal cracking and `1976.066` high-frequency energy).
* **Sub-Harmonic Mantle Bed:** Low-pass filtered envelope follower driving a clean sine/triangle sub-oscillator (`32–55 Hz`), delivering physical weight without muddying the mix.

#### 2. Modal & Material Resonance Bank (*ANMO / Kamchatka* Heritage)
The Moon's seismic signature is famously bell-like. Rather than generic synth chords, seismic amplitude impulses and slope changes excite a bank of **tuned resonant bandpass filters / modal resonators** modeled on physical lunar and spacecraft materials:
* **Anorthosite / Basalt Crust Mode:** Dark, glassy, inharmonic partials with long decay (`Q` scaled to lunar scattering).
* **LM Titanium / Kapton Hull Mode (specifically for S11 `1969.202` & S12 `1969.324`):** Metallic, hollow percussive resonances triggered by astronaut footsteps, hammer strokes, liftoff, and the LM Ascent Stage impact.
* **Custom Harmonic Scales:** Tuning the resonator bank to open intervals (e.g., Lydian Pentatonic, Quartal, or microtonal ratios derived from lunar normal-mode frequency spacing).

#### 3. Seismic-Driven FM & Spectral Phase Vocoding
* **FM Monochrome Engine (*ANMO* heritage):** Use instantaneous seismic velocity (first derivative) to modulate FM modulation index and carrier-to-modulator ratio. Gentle background noise yields pure sine tones; violent shallow moonquakes (`1973.072`) bloom into rich, complex FM sidebands.
* **Phase Vocoder Time-Stretching:** Decouple pitch from playback speed so a `30×` time-compressed event can be frozen or stretched cleanly across a harmonic drone without metallic chipmunk artifacts.

#### 4. Bespoke Lunar Parameter Mappings (Replacing Terrestrial Depth)
In *ShadowZone*, **focal depth** maps to reverb wet/delay time (`35–70%` wet) because deep Earth mantle events travel further. Since shallow moonquakes and impacts occur near the surface (0–200 km) and scattering—not depth—creates lunar spaciousness, **replace depth mapping with lunar-specific geophysical features**:

| Seismic Feature Extracted | Musical / DSP Target | Sonic Rationale |
| :--- | :--- | :--- |
| **Coda Envelope Rise-to-Decay Ratio** | Reverb Diffusion & Delay Feedback | Captures the Moon's unique slow emergent onset and 50-minute ringing tail. |
| **High-to-Low Band Energy Ratio** | Spectral Tilt / Filter Cutoff / FM Index | Distinguishes dull distant L-events from sharp high-frequency impacts (`1976.066`). |
| **Signal Kurtosis / Crest Factor** | Granular Crystal Trigger Threshold | Separates impulsive astronaut footsteps/thermal cracks from smooth emergent swells. |
| **Telemetry Spike / Glitch Density** | Tape Glitch / Bit-Reduction Blend | Turns 1969 S-band transmission imperfections into rhythmic micro-texture. |
| **Solar Elevation / Lunar Time-of-Day** | Harmonic Brightness & Stereo Width | Shifts timbre from cold lunar dawn (`1969.202`) to blazing, crackling lunar noon (`1969.214`). |

---

## 5. Dataset-by-Dataset Sonic & Composition Blueprint

Each of the six selected files occupies a distinct role in the instrument/work:

| # | Dataset ID & Date | Event Character | Pre-Processing Priority | Suggested Time Scale | Primary Synthesis Voice | Dramatic / Musical Role |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | `xa.s11.00.mhz.1969.202` *(21 Jul 1969)* | EASEP turn-on (~04:39 UTC), EVA footsteps, hammer strokes, LM liftoff (17:54 UTC). | Keep EVA & liftoff transients intact; isolate telemetry dropouts/calibration steps to Bus B. | Dual-speed: Real-time/4× around EVA & liftoff; fast-scan quiet gaps. | **Modal Titanium/Regolith Percussion + Found-Sound Telemetry** | **Overture / Human Touch:** Tactile, close-mic'd, rhythmic footprints and rocket ignition shockwave. |
| **2** | `xa.s11.00.mhz.1969.207` *(26 Jul 1969)* | Mid-first-lunar-day; LM noise subsided; ~4 clean L-events & early thermal quakes. | Aggressive despiking & low-level noise-floor normalization to highlight quiet L-events. | `30×–60×` continuous drift. | **Sparse Granular Wash + Soft Sine/Triangle FM** | **Contemplative Drift:** Minimalist space; establishing the Moon's quiet baseline vs. subtle emergent swells. |
| **3** | `xa.s11.00.mhz.1969.214` *(2 Aug 1969)* | Approaching lunar noon; elevated thermal cracking and sustained ringing. | Separate sharp thermal pops (Bus B/transient detector) from slow thermal drift. | `20×–40×` with transient micro-triggers. | **High-Density Crystal Granular Cloud + Glass/Basalt Resonators** | **Thermal Tension:** Shimmering, brittle high-frequency crackles over slowly warming drones. |
| **4** | `xa.s12.00.mhz.1969.324` *(20 Nov 1969)* | Controlled LM Ascent Stage impact at **22:17:17.7 UTC** (~76 km away); 7-min build, 50–55 min decay. | Tight windowing around 22:10–23:30 UTC; zero-clipping headroom calibration for peak. | `8×–12×` (compresses ~65 min impact into ~5–8 min centerpiece). | **Full Hybrid Engine (Impact Modal Bell + 3-Layer Granular + Sub-Bass)** | **The Centerpiece ("The Bell"):** Unmistakable narrative arc—sharp strike, massive emergent bloom, hypnotic hour-long decay. |
| **5** | `xa.s12.00.mhz.1973.072` *(13 Mar 1973)* | **YN-SMQ-9**, huge natural shallow moonquake ($M_b > 6.5$). | Careful dynamic scaling so peak energy drives saturation/FM cleanly without digital clipping. | `12×–24×` focused on event window. | **Deep FM Monochrome + Sub-Harmonic Mantle Drone + Wide Coda Reverb** | **Tectonic Climax:** Massive, visceral, deep planetary power; dense harmonic saturation. |
| **6** | `xa.s12.00.mhz.1976.066` *(6 Mar 1976)* | **YN-SMQ-26**, large shallow moonquake ($M_b > 6.1$) with strong high-frequency content. | Preserve upper `1–3.3 Hz` band energy; match loudness profile with `1973.072`. | `12×–24×` focused on event window. | **Spectral Phase Vocoder + Bright Anorthosite Modal Bank** | **Crystalline Finale:** High-clarity, radiant overtone counterpoint to `1973.072`, resolving into stillness. |

---

## 6. Visual, Spatial & Interface Design (`S3D` / WebGL / Multi-Channel)

Extending the visual language of `seis.monster/mars/` (`S3D`) and SOS's minimalist telemetry interfaces:

### Visual Design Options (Browser / Projection)
1. **3D Selenographic Telemetry Sphere (`S3D` WebGL):**
   * Render a wireframe/topographic Moon with high-precision landing markers for **Apollo 11 (Tranquility Base)** and **Apollo 12 (Ocean of Storms)**, plus the **LM impact site (~76 km from S12)**.
   * Drive surface vertex displacement and radiating concentric wave rings directly from the live audio-rate envelope and spectral centroid of the active `MHZ` trace.
   * Overlay minimalist mission metadata: Station ID (`XA.S11` / `XA.S12`), Julian Day, UTC clock, Solar Illumination Angle, and active synthesis voice counts.
2. **24-Hour Circular / Helical Seismogram Ribbon:**
   * Rather than a flat horizontal bar, map each 24-hour file onto a **rotating lunar dial or helix** so listeners visually anticipate upcoming events (e.g., watching the `22:17:17.7` impact peak approach on the `1969.324` ring) while observing the 50-minute decaying coda trail behind the playhead.

### Spatial Audio Options
* **Binaural / Stereo Web Engine:** Pan S11 and S12 streams slightly asymmetrically or map frequency bands across stereo width (`Filter Cutoff` and `Stereo Width` modulated by coda energy, as in *ANMO*/*Kamchatka*).
* **Multi-Channel Installation / Dome Mode (AlloSphere Heritage):** Split the four DSP layers across discrete speaker arrays (e.g., sub-bass transducers under seating for `20–50 Hz` seismic fundamentals, perimeter ring for the circulating granular coda cloud, overhead zenith speakers for high-frequency thermal crystal grains).

---

## 7. Technical System Architecture Options

### Option 1: Offline Pre-Conditioned Assets + Pure Client-Side Web Audio Engine *(Recommended for `seis.monster/moon/`)*
* **Offline Pipeline (Data Prep):** Use Python/ObsPy once against the six PDS `.mseed` files to verify headers, apply despiking/detrending, extract Clean vs. Telemetry-Residual buses, pre-compute envelope/spectral feature curves, and export compact, browser-ready binary `Float32` waveform chunks + JSON event-bookmark manifests.
* **Client Runtime:** Zero-dependency HTML5 + Web Audio API (`AudioWorklet` for click-free granular/FM synthesis) + WebGL (`Three.js` or custom shaders for `S3D` visuals).
* **Pros:** Instant loading, guaranteed immunity to MiniSEED edge cases or CORS issues on PDS servers, deterministic musical calibration across all 6 files, effortlessly hostable on static CDNs.

### Option 2: Live Client-Side MiniSEED Streaming & Parsing *(Pure SOS Heritage)*
* **Runtime Pipeline:** Port/extend SOS's JavaScript MiniSEED parser to fetch raw `.mseed` byte ranges directly from PDS/EarthScope HTTP endpoints (or an S3 mirror) and decode `FLOAT64`/integer records on the fly in Web Workers.
* **Pros:** Radical data authenticity ("live-decoded directly from NASA PDS binary"), extensible to any day in the 1969–1977 `XA` catalog without re-running offline scripts.
* **Cons:** Requires robust browser-side handling of 4096-byte PDS records, `6.625 Hz` timestamp gaps, and client-side despiking before audio buffers fill.

### Option 3: Studio / Performance Hybrid (Python-OSC to DAW / SuperCollider / Max/MSP)
* **Pipeline:** Stream parsed MiniSEED arrays and extracted geophysical feature vectors via OSC into SuperCollider, Max/MSP, or Ableton/VCV Rack.
* **Pros:** Unmatched audio fidelity (`96 kHz / 32-bit` floating point), hardware modular/sub-bass integration, ideal for producing a mastered 6-movement release or live audiovisual performance.

---

## 8. Phased Implementation Roadmap

### Phase 1: Data Audit & Geophysical Feature Extraction
* Download and inspect all six `XA.*.00.MHZ` MiniSEED files from the PDS archive.
* Document exact sample counts, gap locations, calibration pulses, peak amplitudes, and signal-to-noise floors for each day.
* Validate despiking thresholds (comparing raw vs. cleaned audio behavior) so real events (like S11 EVA footsteps on `1969.202`) are never mistakenly suppressed as digital glitches.

### Phase 2: Single-Event DSP Prototype (`S12 1969.324` Controlled Impact)
* Use the `1969.324` LM Ascent impact (`22:17:17.7 UTC`) as the benchmark calibration file—it has the cleanest narrative arc (sharp onset $\rightarrow$ 7-minute rise $\rightarrow$ 55-minute coda).
* Build and audition the 4-layer synthesis engine (Granular Coda + Modal Resonance + FM + Sub-Bass) in an `AudioWorklet` sandbox, locking in time-compression ratios and scattering-to-reverb mappings.

### Phase 3: Full Six-Dataset Calibration & Contrast Tuning
* Apply the prototype engine across the remaining five files (`202`, `207`, `214`, `072`, `066`).
* Tune per-file presets/profiles so `1969.214` (thermal noon), `1973.072` (massive shallow quake), and `1969.202` (EVA/liftoff) sound unmistakably distinct while belonging to a unified sonic world.

### Phase 4: `S3D` Visual Interface & Interactive Controls
* Integrate the WebGL lunar visualization (`S3D` aesthetic) synced to `AnalyserNode` and seismic playhead metadata.
* Add curated "Mission Bookmarks" (clickable timestamps for Turn-on, EVA, Liftoff, Impact, Peak Coda) alongside free-scrubbing and synthesis macro sliders.

### Phase 5: Listening Tests, Mastering & Deployment
* Conduct loudness normalization (LUFS consistency across quiet vs. $M_b > 6.5$ files), sub-bass translation checks (headphones vs. studio monitors vs. subwoofers), and scientific-storytelling verification against NASA/JPL event logs.

---

## 9. Key Design Decisions to Confirm Before Build

1. **Format Priority:** Should **Phase 1–4** target an interactive web instrument (`seis.monster/moon/`), a fixed 6-movement recorded work, or both (web instrument + studio stems export)?
2. **Authenticity vs. Musicality Balance:** Should harmonic tuning (e.g., modal resonator pitches) remain locked to a cohesive musical scale across all 6 days, or drift microtonally according to each event's raw FFT spectral peaks?
3. **Glitch Philosophy:** Should 1969 telemetry bit-errors and calibration pulses be **silently scrubbed** (pure geophysical focus) or **exposed as an optional mixable layer** ("Found Telemetry")?
4. **Temporal Scope:** Should playback cover **full 24-hour scans** (via dynamic time-warping) or **curated 30–90 minute event windows** centered on the cataloged moonquakes and impacts?
