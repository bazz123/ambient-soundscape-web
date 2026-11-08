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
4. **Save the results.** Name a take, then click *Record take* once to start and again to finish. Takes are saved as `.webm` (`.mp4` in Safari).

The stage at the top is a grain map: each dot is a grain or a shimmer, placed by where in the essence it came from (left to right) and by its pitch (up and down). Warm bands show the drone voices, the vertical line is the scan head with its spray range, and the thin line marked "freeze" is the freeze pad.

## Run locally

Open `index.html` in a browser. Audio stays in your browser; nothing is uploaded.

## Credits

The original concept, design and desktop app are by **Dimitra Maria Pavlou**, made during an internship at Groves Sound Branding GmbH, with Christoph Groß-Fengels and the Groves team. This repo is an independent web reimplementation and contains none of the original app's code.
