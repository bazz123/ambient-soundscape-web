# Ambient Soundscape Generator (web)

A browser version of the [Ambient Soundscape Generator](https://github.com/dmpavlou/Ambient-Soundscape-Generator) by Dimitra Maria Pavlou. It's a single HTML file built on the Web Audio API, with no build step, server or dependencies.

**Try it:** https://bazz123.github.io/ambient-soundscape-web/

## How it works

1. **Load a sound.** Drop in an `.mp3`, `.wav`, `.aif`, `.aiff` or `.flac` file. The loudest 3 seconds are selected for you. A generated chime sample is loaded at start, so you can try it right away.
2. **Capture the essence.** Play the source and press *Capture essence* once to start and again to stop, or drag across the waveform. About 3 seconds works well.
3. **Press START.** The essence feeds four layers:
   - **Grain cloud:** short, Hann-windowed fragments taken around a moving **scan head**. *Scan speed* moves it through the essence (0 holds one spot, 1× is original speed, negative runs backwards), *Spray* scatters grains around it, and *Grain size* and *Pitch jitter* (in cents) shape each grain.
   - **Slow drone:** the essence looped at half and quarter speed through slowly moving low-pass filters, high-passed at 35 Hz.
   - **Shimmer:** reversed fragments an octave or more up, fading in and out.
   - **Freeze pad:** a spectral freeze. The spectrum at the scan head is captured with an FFT and resynthesized in an AudioWorklet with fresh random phases every hop, giving an endless pad with no loop seam. It refreezes on every scene change, or on demand.

   Everything then runs through a **tape stage** (wow, drift and flutter on a modulated delay, tanh saturation with a small head bump, and hiss), a reverb with pre-delay and early reflections, and a filtered echo. Every 10–35 seconds (set by *Drift*), the soundscape glides to a new root and interval set.

   The master chain is a glue compressor, a fast limiter and an oversampled soft clipper with a −1 dBFS ceiling. A K-weighted loudness meter (momentary, short-term, integrated, peak) has targets of −23, −16 and −14 LUFS, and *Match target* sets the output trim. The volume slider only changes what you hear, not what is recorded or measured.
   **Harmony.** Each essence's key is detected: spectral peaks are folded into a chroma vector and matched against the Krumhansl–Schmuckler major and minor profiles (the correlation is shown as a confidence), and the tuning offset in cents is estimated from the strongest in-tune peaks. With *Follow detected key* on and a clear key, scenes walk between I, IV and V (a weighted Markov chain), the grain interval sets use the detected mode's third, and a low *Root tone* on the detected tonic, tuned in cents, sits under the mix.

   **Essence slots.** Up to four slots (A–D), each with its own file and region. Each scene change crossfades to the next slot in order: grains switch over gradually, and drones fade between slots. With *Follow detected key* on, slots B–D are transposed (at most ±6 semitones, plus tuning) to slot A's key.

   **Space.** Choose a generated *hall* (6 s, early reflections, 30 ms pre-delay), *plate* (3.5 s, dense and bright) or *spring* (2 s, trains of descending chirps that lengthen with each pass), or load your own impulse response (WAV/AIFF; up to 2 channels and 12 s, leading silence trimmed). Switching crossfades between two convolvers, so there is no click. *Binaural mode* places grains and shimmer around the listener with HRTF panning: the grain cloud orbits once every 40 s, and distance (1–6 m) is heard as level roll-off plus a closing low-pass. A small top view on the stage shows where sounds are. Drones and pads stay a stereo bed.

   **Form.** An *Arc* shapes the soundscape over the chosen *Length* (0:30–30:00): *Loop* stays steady, *Sunrise* starts sparse and dark and opens up, *Bloom* rises to a peak mid-way and falls back to silence, and *Sleep* thins out and fades to silence. Levels and brightness run as sample-accurate parameter ramps, and grain density follows the same keyframes. The *Seed* drives five separate random streams (grains, shimmer, scenes, placement, drones), and every event is scheduled on audio time, so the same seed and settings play the same soundscape. The seed is kept in the page link (`#seed-12345`).

4. **Export.** *Render and save* runs the same engine in an `OfflineAudioContext`, faster than real time (about 10–15× on a laptop), at your device's sample rate as 24-bit WAV.
   - **Loudness:** the mix is measured to ITU-R BS.1770-4 (K-weighting, gated integrated loudness) and set to the meter's target (−23, −16 or −14 LUFS). True peak, found by 4× windowed-sinc interpolation, is kept at or below −1 dBTP; if that needs more level than the limiter allows, the mix is rendered a second time into the limiter.
   - **Seamless loop** (Loop arc): renders a 12 s pre-roll, the loop and up to 8 s more, then crossfades that extra material into the head (equal power), so the last sample runs straight into the first.
   - **Stems:** grains, drone (with the root tone), shimmer and freeze are rendered with the same seed, at the mix's gain but before the master limiter, so they sum back to the mix apart from limiting.
   - Multi-file exports come as a ZIP with a notes file (seed, settings, measured loudness).

5. **Live takes.** Name a take, then click *Record take* once to start and again to finish. Takes are saved as `.webm` (`.mp4` in Safari).

The stage at the top is a grain map: each dot is a grain or a shimmer, placed by where in the essence it came from (left to right) and by its pitch (up and down). Warm bands show the drone voices, the vertical line is the scan head with its spray range, and the thin line marked "freeze" is the freeze pad.

## Run locally

Open `index.html` in a browser. Audio stays in your browser; nothing is uploaded.

## Credits

The original concept, design and desktop app are by **Dimitra Maria Pavlou**, made during an internship at Groves Sound Branding GmbH, with Christoph Groß-Fengels and the Groves team. This repo is an independent web reimplementation and contains none of the original app's code.
