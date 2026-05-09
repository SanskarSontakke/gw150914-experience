# 🌌 Multisensory Astrophysics: GW150914 Audio-Haptic Experience

**Translating Gravitational Wave Signals into Synchronized Audio-Haptic Experiences for Consumer Mobile Devices.**

This project bridges theoretical astrophysics and modern web accessibility by translating the raw, time-series data of the first observed binary black hole merger (GW150914) into a synchronized audio and haptic experience. It is designed to be experienced directly on consumer Android smartphones via a web browser, requiring zero setup or app installations.

## 🚀 Live Demo
**Experience it here:** [Link to your GitHub Pages site - e.g., https://yourusername.github.io/gw150914-experience] 
*(Note: For the haptic vibration to work, you must open this link on an **Android smartphone**.)*

## 📖 Project Overview

On September 14, 2015, LIGO detected **GW150914**, the ripples in spacetime caused by two black holes colliding over a billion light-years away. The raw data of this event is a microscopic spatial strain ($10^{-21}$ meters). 

While astrophysics typically relies on visual graphs, this project asks: *What if you could feel the fabric of spacetime rippling?*

By utilizing advanced Digital Signal Processing (DSP) and Web APIs, this project maps the exact physical amplitude and frequency of the black hole "inspiral" phase into human-perceptible audio ranges and localized vibration patterns.

## ✨ Features

* **Sample-Accurate Synchronization:** The Web Audio API and HTML5 `navigator.vibrate()` API are perfectly coupled using a custom JavaScript look-ahead scheduler, ensuring millisecond precision between sound and touch.
* **15-Second Crescendo:** The original 0.2-second physical collision was scientifically time-dilated by a factor of 10x to create a dramatic 15-second build-up, allowing human perception to fully register the "chirp" morphology.
* **Perceptual Scaling:** The haptic data utilizes Stevens' Power Law (Gamma correction) to ensure the vibration amplitude feels like a natural physical crescendo to the human nervous system.
* **Mobile-First Accessibility:** Built with an APCA-compliant high-contrast dark theme (#121212) to prevent OLED black-smearing and reduce visual clutter.

## ⚙️ How It Works (The Pipeline)

### 1. Data Processing (Python / Colab)
* Fetched 4096 Hz raw strain data from the Gravitational Wave Open Science Center (GWOSC) using `pycbc` and `gwpy`.
* Applied spectral whitening (Welch's method) and a 20 Hz - 300 Hz zero-phase Butterworth bandpass filter to remove seismic noise and isolate the chirp.
* **Audio Branch:** Executed a Phase Vocoder algorithm to pitch-shift the fundamental frequencies into the optimal human hearing range, exporting as a 44.1 kHz 16-bit `.wav` file.
* **Haptic Branch:** Extracted the amplitude envelope, applied time-dilation, and mathematically simulated varying vibration intensities using Pulse-Width Modulation (PWM) across a 20 ms cycle, exporting as a structured `.json` array.

### 2. Frontend Delivery (Web)
* A lightweight, single-file (`index.html`) web architecture.
* Fetches the `.wav` and `.json` assets asynchronously.
* Connects the hardware-backed `AudioContext` clock with a `requestAnimationFrame` loop to feed the vibration arrays to the mobile actuator in perfect sync with the audio track.

## 📂 Repository Structure

* `index.html` - The core application, UI, and synchronization engine.
* `gw150914_shifted_chirp.wav` - The processed 15-second audio file.
* `gw150914_haptic_pattern.json` - The 15-second PWM vibration array mapped for the Vibrate API.

## ⚠️ Compatibility Note

* **Android:** Fully supported (Chrome, Edge, Firefox, Brave).
* **iOS (iPhone/iPad):** Apple's WebKit currently severely restricts the `navigator.vibrate()` API. iOS users will be able to see the UI and hear the audio, but the haptic feedback will not fire. 
* **Desktop:** Desktops do not have vibration motors. Please use a smartphone for the full experience.

## 🔬 Data Credits

This research uses open data provided by the [Gravitational Wave Open Science Center](https://gwosc.org), a service of LIGO Laboratory, the LIGO Scientific Collaboration, the Virgo Collaboration, and KAGRA.
