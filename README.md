# midi-device-debug

Single-page Web MIDI debug tool. Lists MIDI devices visible to the browser, logs incoming messages, and routes them through one of five sound engines for instant audible feedback.

## Run locally

```bash
python -m http.server 8000
```

Then open <http://localhost:8000/> in Chrome or Edge and click **Request MIDI access**. `localhost` counts as a secure context, so the API works without HTTPS.

## What's in it

- **Device listing** — input and output ports the browser sees, with manufacturer / state / connection info. Hot-plug supported via `onstatechange`.
- **Message log** — decoded note-on/off, CC, pitch bend, aftertouch, program change, channel pressure with timestamps and channel.
- **Sound engines** — pick from the dropdown:
  - **PSG Synth** — one oscillator per voice. Pick from sine, triangle, sawtooth, square, pulse 25%, pulse 12.5%, or noise. Per-waveform gain compensation keeps perceived loudness roughly consistent across timbres; pulses use precomputed `PeriodicWave`s built from each duty cycle's Fourier coefficients.
  - **FM synth** — six sine operators per voice, twelve hand-picked DX-style presets (EP, bell, bass, brass, marimba, harp, strings, whistle, harmonica, tubular, orch hit, pad). Each preset is an algorithm (carrier/modulator topology) plus per-op ratio, level, and ADSR envelope; modulation index β scales with pitch via per-connection gain.
  - **Wavetable synth** — eight-position bank, scrubbed live via a slider that crossfades between adjacent positions. Held notes morph within their locked pair (Web Audio doesn't let you swap a `PeriodicWave` on a running oscillator); new note-ons relock to the current position.
  - **Drums** — three synthesised kits (TR-606, 80s gated, CR-78) sharing a common set of voices (kick, snare, toms, hats, cymbals, clap, cowbell, side stick, ride, tambourine) parameterised per kit. Each kit applies its own master coloration filter (606 highpasses, 80s peaks at 3.5 kHz, CR-78 lowpasses) plus a parallel reverb send into a `ConvolverNode` whose impulse response is synthesised on the fly — for the 80s kit, that IR is truncated and hard-faded at ~120 ms to give the iconic gated tail. Listens on channel 10 only; notes 35–59 trigger GM kit pieces.
  - **Soundfont sampler** — General MIDI sampled instruments streamed on demand from the public [MIDI.js soundfonts CDN](https://github.com/gleitz/midi-js-soundfonts) via [`soundfont-player`](https://github.com/danigb/soundfont-player).
- **Universal MIDI controls** — pitch bend, modulation wheel (CC 1), and sustain pedal (CC 64) work across every engine. The Controllers panel shows live state for all three. Sustain pedal is implemented at the dispatcher layer (deferring note-offs while held), so any engine benefits without code changes.
- **Panic** — top-right red link kills all sounding notes, clears deferred sustain, and resets the Controllers display.

## Browser support

- Chrome, Edge, Opera: native
- Safari 16.4+: native
- Firefox: behind `dom.webmidi.enabled` flag

## Deploy

Push to `main` and enable GitHub Pages (Settings → Pages → Source: `main` / root). The site will be served over HTTPS, which Web MIDI requires.
