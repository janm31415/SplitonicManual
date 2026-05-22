# Splitonic Manual

**Splitonic** is a real-time harmonic/percussive source separation plugin, available as an Audio Unit (AUv3) for iOS and macOS.

It uses the HPSS algorithm - Harmonic/Percussive Source Separation - which works by analysing the short-time Fourier spectrum and applying two median filters: one along the time axis (to isolate percussive transients) and one along the frequency axis (to isolate harmonic tonal content). What is neither harmonic nor percussive ends up in the residual. You get independent gain control over all three components, per channel.

---

## How it works

The input signal goes through an FFT. On the resulting spectrogram, two sliding median filters run in parallel:

- **Horizontal filter** - runs along the time axis. A signal that is consistent over time (i.e. tonal/harmonic) will survive this filter. Transients will not.
- **Vertical filter** - runs along the frequency axis. A signal that is consistent across frequency bins (i.e. broadband/percussive) will survive this filter. Narrow tonal content will not.

Both filtered versions are compared per bin using the separation threshold, and each bin is assigned to harmonic, percussive, or residual. The output is then reconstructed via overlap-add IFFT synthesis.

---

## Parameters

### Left-Mid / Right-Side channel sections

Each channel has its own set of controls. The channel section is labelled **Left-Mid** or **Right-Side** depending on the current processing mode (see Toggles below).

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| **Harm** | ‚àí18 to +18 dB | 0 dB | Gain applied to the harmonic component. Turn it down to ‚àí18 dB to effectively mute it. |
| **Perc** | ‚àí18 to +18 dB | 0 dB | Gain applied to the percussive component. |
| **Res** | ‚àí18 to +18 dB | 0 dB | Gain applied to the residual (what is left after harmonic and percussive separation). |
| **Thres** | 1 to 10 | 2 | Separation threshold. Higher values make the separation more aggressive ‚Äî bins need a stronger dominance to be classified as harmonic or percussive. Lower values classify more bins, leaving less in the residual. |

### Filter length section

These control the size of the median filters used in the separation algorithm.

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| **H Filter L/R** | 10 to 400 ms | 200 ms | Length of the horizontal (time-axis) median filter. Longer values give better harmonic/percussive separation but introduce more artefacts on fast transients. |
| **V Filter L/R** | 100 to 1000 Hz | 500 Hz | Bandwidth of the vertical (frequency-axis) median filter. Wider values improve percussive separation at the cost of smearing tonal detail. |

### Master section

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| **Gain L/R** | ‚àí18 to +18 dB | 0 dB | Output gain for left and right channels. |
| **Mix** | 0 to 100 % | 100 % | Dry/wet mix. At 100 % you hear only the processed signal. At 0 % you hear only the original dry signal. |

### Latency

Sets the FFT hop size, which directly determines the processing latency. Smaller latency = smaller FFT hops = more artefacts at aggressive separation settings.

| Setting | Latency |
|---------|---------|
| 5 ms | Lowest latency, lowest CPU |
| 10 ms | |
| 21 ms | |
| 42 ms | Default |
| 85 ms | Highest latency, highest CPU |

### Toggles

| Toggle | Options | Description |
|--------|---------|-------------|
| **Mode** | Left-Right / Mid-Side | Switches the processing domain. In Mid-Side mode, the Left channel controls apply to the Mid signal and the Right channel controls apply to the Side signal. |
| **Link** | Link / Unlink | When linked, the left and right channel parameters are coupled ‚Äî changing one side also changes the other. |

---

## Spectrum meter

The spectrum meter at the top shows the RMS energy per frequency band, separately for both channels (or Mid/Side depending on mode). This gives a quick overview of how the energy in your signal is distributed across the spectrum, and can help you tune the filter settings.

---

## Presets

Use the **PRESETS** button to save and load presets. The **‚Äπ** and **‚Ä∫** arrows step through presets sequentially. Presets store all parameter values.

---

## Tips

- **Isolating drums from a mix**: set Harm and Res gains low (or mute them at ‚àí18 dB), keep Perc at 0 dB. Adjust the horizontal filter length until you get clean transient isolation.
- **Removing drums / isolating tonal content**: mute the percussive component, keep harmonic up.
- **Mid-Side mode**: useful for stereo width processing. The Mid signal typically contains most of the mono content (vocals, kick, bass), while the Side carries room and stereo elements. Separating harmonics from percussion in each of those independently gives quite a bit of creative control.
- **Latency vs quality**: for mixing and mastering, use 42 or 85 ms for the cleanest separation. For live processing where latency matters, 5 or 10 ms is acceptable but expect some separation artefacts.
- **Threshold**: start at 2 (default). If the separation sounds blurry or components bleed into each other, raise it. If the output sounds thin or ringy, lower it.

---

## Platform support

- **iOS**: AUv3 Audio Unit, works in any AUv3-compatible host (AUM, GarageBand, Cubasis, ‚Ä¶)
- **macOS**: AUv3 Audio Unit, works in Logic Pro, GarageBand, Reaper, and other compatible hosts

---

## License

See the in-app Acknowledgements for third-party library credits.
