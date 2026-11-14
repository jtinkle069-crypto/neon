# Neon Drip Trial: Wavetable Synth (VST3 for FL Studio)

This project builds **Neon Drip Trial**: the full synth, but it goes silent **1 hour after it is first opened** on that computer. A countdown shows in the header.

## Features
- **2 wavetable oscillators**, each with 16 wavetables of 16 morph frames: Basic Morph, PWM Square, Sync Saw, Vowel Formant, Organ Bars, FM Bell, FM Growl, Harmonic Sweep, Wave Fold, Bit Crush, Odd Hollow, Dark to Bright, Phase Distort, Comb Saw, Chaos Spectra and Tanh Drive. The tables are band-limited, so high notes don't alias. Each oscillator has Position, Level, Semitone and Fine controls.
- **Filter** with Low Pass, Band Pass and High Pass modes, plus Cutoff, Resonance and Drive (saturation).
- **Amp envelope and Mod envelope** (ADSR), each with a live curve display. The Mod envelope sweeps the filter cutoff, in either direction.
- **LFO** with Sine, Triangle, Saw, Square and S&H shapes. It can modulate Cutoff, Osc 1 Position, Osc 2 Position, Both Positions, Pitch or Amp.
- **4 Macros:** MORPH (wavetable position), BRIGHT (filter), MOTION (LFO depth) and DRIVE (saturation).
- **EQUALIZER page** with 7 bands: low cut, low shelf, 3 bell bands, high shelf and high cut. There is a live response curve with draggable nodes; scroll over a bell node to change its width (Q).
- 16-voice polyphony, pitch bend (±2 semitones), and an on-screen matte black keyboard.

## Building on Windows (for FL Studio)
1. Install **Visual Studio 2022** with the "Desktop development with C++" workload, then **CMake** and **Git**.
2. Open a terminal in this folder and run:
   ```
   cmake -B build -G "Visual Studio 17 2022"
   cmake --build build --config Release
   ```
   The first configure downloads JUCE 8 automatically, which takes a few minutes.
3. Copy the `.vst3` folder to `C:\Program Files\Common Files\VST3\`. They are here:
   - `build\NeonDripTrial_artefacts\Release\VST3\Neon Drip Trial.vst3`
4. In FL Studio, go to **Options → Manage plugins → Find installed plugins**. Then add "Neon Drip Trial" from the plugin browser or the Channel Rack.

Standalone `.exe` versions are built as well, in `...\Release\Standalone\`.

## Building on macOS
```
cmake -B build -G Xcode
cmake --build build --config Release
```
Copy the `.vst3` bundle to `~/Library/Audio/Plug-Ins/VST3/`.

## About the trial
- The first time the trial plugin is opened, it writes a timestamp file:
  - Windows: `%APPDATA%\NeonDrip\.ndtrial`
  - macOS: `~/Library/NeonDrip/.ndtrial`
- After 3600 seconds the plugin outputs silence and the header shows **TRIAL OVER**.
- To change the length, edit `trialLengthSeconds` in `Source/PluginProcessor.h`.
- **This is a simple time lock, not copy protection.** Deleting the file restarts the trial. If you plan to sell the plugin, use a licensing service, such as a license-key server, or a framework such as Moonbase or Keyzy.

## Source layout
| File | Contents |
|---|---|
| `Wavetables.*` | Generates the wavetables with FFT band-limiting (8 mip levels) |
| `PluginProcessor.*` | Synth voices, LFO, envelopes, filter, EQ, parameters, trial timer |
| `EqDesign.h` | EQ band maths, shared by the audio engine and the display |
| `NeonLookAndFeel.*` | Neon purple knobs, menus and buttons |
| `Widgets.*` | Knobs, wavetable display, envelope display, EQ display |
| `DripOverlay.*` | The poured purple liquid, drips and animated droplets |
| `PluginEditor.*` | Page layout, tabs, keyboard |
