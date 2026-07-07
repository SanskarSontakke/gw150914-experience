# GW150914: Multisensory Astrophysics Experience

> Turns raw gravitational wave strain data from the first observed black hole merger into synchronized audio, haptic, and visual feedback for mobile devices.

**[Live demo](https://sanskarsontakke.github.io/gw150914-experience/) — Android phones recommended for full haptic experience.**

![License: MIT](https://img.shields.io/badge/license-MIT-blue) ![Python 3.7+](https://img.shields.io/badge/python-3.7%2B-blue)

## What it does

On September 14, 2015, LIGO detected GW150914: the gravitational waves from two black holes colliding over a billion light-years away. This project converts the raw strain data (dimensional scale: 10^−21 meters) into a real-time, synchronized audio-visual-haptic experience.

The workflow:
1. **Data processing**: Fetch raw strain from GWOSC, apply spectral whitening, bandpass filter (20–300 Hz), and isolate the 1.3-second inspiral + ringdown.
2. **Audio synthesis**: Time-stretch to 15 seconds, pitch-shift 10×, resample to 44.1 kHz, and export as WAV.
3. **Haptic envelope**: Extract amplitude via Hilbert transform, downsample to 20 Hz, apply perceptual scaling (Stevens' Power Law), and export as JSON.
4. **Frontend sync**: Use an HTML5 video as a master clock to lock audio and haptic output to precise video frames, allowing scrubbing and pausing while maintaining sync.

## Why I built it

I wanted to answer the question: *What if you could hear and feel spacetime rippling?* This is a learning project that combines gravitational physics (raw LIGO data), digital signal processing (whitening, filtering, Hilbert transforms), audio engineering (phase vocoding, pitch shifting), and web APIs (Vibration API, Web Audio API) into one integrated experience.

## Tech stack

- **Data acquisition & processing**: Python 3, NumPy, SciPy, gwpy, pycbc, librosa
- **Frontend**: HTML5, vanilla JavaScript, Web Audio API, Vibration API
- **Platform**: Deployed as a single-file HTML + remote assets to GitHub Pages

## Getting started

**Clone and explore:**
```bash
git clone https://github.com/SanskarSontakke/gw150914-experience.git
cd gw150914-experience
```

**To run the web app locally:**
```bash
python -m http.server 8000
# Visit http://localhost:8000 in your browser (Android for haptic feedback)
```

**To regenerate the audio and haptic data** (requires `pycbc`, `gwpy`, `librosa`):
```bash
pip install numpy scipy librosa resampy gwpy==4.0.1 pycbc==2.11.0
jupyter notebook gw150914.ipynb
# Run all cells to fetch data, process, and export new audio/haptic files
```

## How it works

### Data flow

1. **Raw strain (GWOSC):** 32-second window centered on GPS time 1126259462.4, sampled at 4096 Hz.
2. **Preprocessing:** Apply Tukey window, compute PSD via Welch's method, whiten in frequency domain, apply zero-phase Butterworth bandpass filter (8th order, 20–300 Hz).
3. **Chirp isolation:** Crop to 1.3 seconds (1.2 s inspiral + 0.1 s ringdown) centered at merger.
4. **Audio branch:** Time-stretch to 15 s (stretch factor ≈ 0.0867), pitch-shift +60 semitones (10× frequency), resample to 44.1 kHz, peak-normalize, quantize to 16-bit PCM.
5. **Haptic branch:** Extract amplitude envelope via Hilbert transform, smooth with Savitzky-Golay filter, downsample to 20 Hz (300 samples), normalize, apply gamma=0.5 for perceptual scaling, export as JSON.
6. **Frontend sync:** Video plays at 1.0×, triggers audio playback at t=0, fires haptic PWM pulses at 50 ms intervals using the 20 Hz envelope. User can scrub video; audio and haptic seek in lockstep.

### Key technical choices

- **Spectral whitening:** Removes colored noise inherent to the LIGO detector, making the signal clearer.
- **Phase vocoder:** Preserves formants during time-stretch, avoiding the "Mickey Mouse" effect of naive pitch-shifting.
- **Hilbert transform for envelope:** Extracts the true amplitude of the analytic signal without aliasing.
- **Stevens' Power Law (γ=0.5):** Haptic amplitude perception is nonlinear; gamma correction makes the physical crescendo feel natural.
- **20 Hz haptic sampling:** Coarse enough to fit in a single JSON array, fine enough to represent the chirp envelope dynamics.
- **Master-clock video:** Using video timestamps as the authoritative time source avoids audio/haptic drift; users can pause, scrub, and resume without re-sync.

## Data & reproducibility

### Source data

The raw gravitational wave strain comes from **GWOSC** (Gravitational Wave Open Science Center):
- **Detector:** LIGO Hanford (H1)
- **Event:** GW150914 (binary black hole merger)
- **GPS time of merger:** 1126259462.4 (September 14, 2015 at 09:50:45 UTC)
- **Raw data:** 32-second window, 4096 Hz sampling rate
- **Access:** Fetched via `gwpy.timeseries.TimeSeries.fetch_open_data()` in the notebook

### Processed artifacts in this repo

- **`gw150914_shifted_chirp.wav`:** 15-second audio, 44.1 kHz, 16-bit stereo. Includes the full time-stretched inspiral + ringdown with pitch-shifted frequencies.
- **`gw150914_haptic_envelope.json`:** 300-element floating-point array (20 Hz × 15 s), normalized to [0, 1], with gamma correction applied.
- **`gw150914_visual.mp4`:** 42-second numerical relativity simulation from SXS. Collision occurs at t ≈ 13–15 s within the video.
- **`metadata.json`:** Synchronization landmarks: inspiral (0–13.846 s), merger (13.846–14.2 s), ringdown (14.2–15.0 s).

### Reproduction steps

1. **Set up environment:**
   ```bash
   pip install numpy scipy librosa resampy gwpy==4.0.1 pycbc==2.11.0
   ```

2. **Run the notebook:**
   ```bash
   jupyter notebook gw150914.ipynb
   ```
   Execute all cells in sequence. The notebook will:
   - Fetch H1 strain data for the 32-second window
   - Apply windowing, compute PSD, whiten, and bandpass filter
   - Isolate the 1.3-second chirp window
   - Export `gw150914_shifted_chirp.wav` (audio) and `gw150914_haptic_envelope.json` (haptic) to the repository root

3. **Verify outputs:**
   ```bash
   ls -lh gw150914_shifted_chirp.wav gw150914_haptic_envelope.json metadata.json
   ```
   - Audio file: ~1.3 MB, 15 seconds at 44.1 kHz
   - Haptic JSON: ~2.4 KB, 300 samples

4. **Test locally:**
   ```bash
   python -m http.server 8000
   # Open http://localhost:8000 on an Android phone
   ```

### Notes on reproducibility

- Data fetching depends on GWOSC availability and network connectivity.
- The exact waveform may vary slightly if GWOSC updates their calibration or data processing.
- Audio quality depends on scipy/librosa versions; newer versions may produce slightly different spectral characteristics.
- The notebook is designed to run in Google Colab (includes `!pip install`) but also works locally.

## Results & status

**Status:** Working demo. The web app successfully synchronizes audio, haptic, and video output on Android smartphones. Users can play, pause, and scrub the event while maintaining precise sync across all three modalities.

**Platform support:**
- **Android (Chrome, Edge, Firefox, Brave):** Full audio + visual + haptic.
- **iOS (Safari, Firefox, Chrome):** Audio + visual only; Apple's WebKit severely restricts the Vibration API.
- **Desktop:** Audio + visual; no vibration motor.

**Performance:** Runs on commodity smartphones with no lag or dropped frames. Haptic PWM updates fire at 50 ms intervals, matching the typical refresh rate of mobile browsers.

## Credits

- **Scientific data:** GWOSC (Gravitational Wave Open Science Center), a service of LIGO Laboratory, LIGO Scientific Collaboration, Virgo Collaboration, and KAGRA.
- **Visual simulation:** Simulating eXtreme Spacetimes (SXS) Project and Max Planck Institute for Gravitational Physics.
- **Methodology:** GW150914 discovery paper (Abbott et al. 2016, *Physical Review Letters*, doi: 10.1103/PhysRevLett.116.061102).

## License

MIT © 2026 Sanskar Sontakke
