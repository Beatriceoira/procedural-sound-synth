# Procedural Sound Synth

A browser-based ambient sound generator built on the native **Web Audio API**. It creates brown noise, rain and binaural beats entirely in code, with **zero external audio files** and zero dependencies.

## Features

- **Brown noise**: procedurally generated 1/f² noise with an adjustable lowpass tone control.
- **Rain**: a filtered white-noise bed plus randomly timed, randomly panned droplets (short sine blips shaped by gain envelopes). Intensity sets drops per second.
- **Binaural beats**: two hard-panned `OscillatorNode`s (left carrier, right carrier + beat) with Delta, Theta, Alpha and Beta presets.
- **Mixer**: per-layer level, per-layer on/off, master volume.
- **Live visualizer**: frequency bars driven by an `AnalyserNode`.
- Light and dark theme follow the system setting.

## Run it

No build step. Open `index.html` in a modern browser (Chrome, Edge, Firefox, Safari) and click **Start audio**. Browsers block audio until a user gesture, so nothing plays before that click.

To serve it locally instead:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Binaural beats: how to use

Binaural beats only work with **stereo headphones**. The left ear gets the carrier frequency and the right ear gets carrier + beat, and your brain perceives the difference as a slow pulse.

| Preset | Beat | Typical use |
|--------|------|-------------|
| Delta  | 2 Hz  | Sleep |
| Theta  | 6 Hz  | Calm, meditation |
| Alpha  | 10 Hz | Relaxation |
| Beta   | 20 Hz | Focus |

The brainwave associations are popular conventions; evidence for their effects is mixed. Keep the volume low.

## How it works

```
BufferSource (brown) -> Lowpass -> Gain -+
BufferSource (white) -> HP -> LP -> Gain -+
Oscillator drops     -> Gain -> Panner ---+--> Master Gain -> Analyser -> Destination
Oscillator L -> Panner(-1) -+
Oscillator R -> Panner(+1) -+-> Gain ------+
```

- Noise is a 4-second looping `AudioBuffer` filled in JavaScript. Brown noise is white noise run through a leaky integrator: `last = (last + 0.02 * white) / 1.02`.
- Parameter changes use `setTargetAtTime` so sliders never click or pop.
- Rain droplets are scheduled with exponentially distributed random intervals, which sounds like a natural Poisson process.

## Project structure

```
index.html   # the whole app (HTML, CSS, JS)
README.md
.gitignore
```

## Ideas for extension

- Sleep timer with a fade-out
- Pink and white noise layers
- Thunder and wind layers
- Save and load mixer presets with `localStorage`
- Isochronic tones as an alternative to binaural beats

## License

MIT. Use it however you like.
