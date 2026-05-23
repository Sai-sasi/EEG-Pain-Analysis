# EEG Pain Analysis — Laser Evoked Potentials
## Python · MNE-Python · OpenNeuro ds005284

**Author:** Sai Sasi Sekhar Kongala
**Institution:** University of Glasgow — MSc Brain Sciences (2024–2025)
**GitHub:** github.com/Sai-sasi
**Status:** ✅ sub-001 complete | ✅ 26-subject group analysis complete

---

> ⚠️ **Data Attribution Notice**
> This repository contains **analysis code and output figures only**.
> The raw EEG data used in this project was collected and published
> by Xiangyue et al. (2024/2025) and is available publicly on
> OpenNeuro under open access terms. Raw data files are **not included**
> in this repository. See [Data Source](#data-source-and-attribution)
> for full citation and download instructions.

---

## Overview

This project implements a complete Python-based EEG analysis pipeline
for processing nociceptive laser-evoked potential (LEP) data across
**all 26 participants** of OpenNeuro dataset ds005284. The pipeline
extracts cortical pain biomarkers — alpha band oscillations and N2/P2
LEP components — from 64-channel BioSemi EEG recordings made during
laser pain stimulation.

This work is motivated by my MSc dissertation (Kongala, 2025,
University of Glasgow) which characterised cold-selective spinal
projection neurons using patch-clamp electrophysiology. This project
asks the translational question: do those spinal nociceptive circuits
produce measurable cortical oscillatory signatures in human EEG?

---

## Data Source and Attribution

### Primary Dataset Citation

**Paper:**
Xiangyue, Z. et al. "A comprehensive EEG dataset of laser-evoked
potentials for pain research."
*Scientific Data* 12, 1536 (2025).
DOI: [10.1038/s41597-025-05900-1](https://doi.org/10.1038/s41597-025-05900-1)

**Dataset:**
Xiangyue, Z. et al. "26 By Biosemi." *OpenNeuro* (2024).
DOI: [10.18112/openneuro.ds005284.v1.0.0](https://doi.org/10.18112/openneuro.ds005284.v1.0.0)

**Download:** 👉 [openneuro.org/datasets/ds005284](https://openneuro.org/datasets/ds005284)

### Data Licence

OpenNeuro datasets are shared under the
[Creative Commons CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/)
licence — dedicated to the public domain. Attribution is not legally
required under CC0 but is strongly encouraged as good scientific
practice and is provided throughout this repository.

### What This Repository Contains

| Item | Included | Notes |
|------|----------|-------|
| Analysis code — sub-001 (.ipynb) | ✅ Yes | Single subject pipeline |
| Analysis code — EEG-Pain-Project-Sub-26 (.ipynb) | ✅ Yes | All 26 subjects |
| Output figures — sub-001 (.png) | ✅ Yes | 5 figures |
| Output figures — group (.png) | ✅ Yes | Distribution plots |
| Group results (.csv) | ✅ Yes | N2, P2, alpha — 26 subjects |
| Raw EEG data (.bdf) | ❌ No | Download from OpenNeuro |

---

## Dataset Description

The ds005284 dataset contains 64-channel EEG recorded from
26 young participants (average age 21 years, 18 female).
Each participant received 16 fixed-intensity laser stimuli
delivered at approximately 20-second intervals.

| Parameter | Value |
|-----------|-------|
| EEG system | BioSemi ActiveTwo |
| Channels | 64 EEG + 1 Status |
| Sampling rate | 1024 Hz |
| Participants | 26 healthy adults |
| Stimuli | Nociceptive CO₂ laser to dorsum of hand |
| Event ID | 54 (laser stimulus, 16 per subject) |
| Format | BIDS-compliant (.bdf) |
| Total size | ~1.6 GB |

---

## Analysis Pipeline

```
Raw BDF file (OpenNeuro ds005284)
         ↓
Bad channel detection and removal (A2)
         ↓
Average reference applied
         ↓
Bandpass filter: 1–40 Hz
         ↓
Notch filter: 50 Hz (mains noise)
         ↓
┌──────────────────────┬────────────────────────┐
│   Alpha Band         │   Laser Evoked         │
│   Analysis           │   Potential            │
│                      │                        │
│   Compute PSD        │   Event detection      │
│   Extract 8-12 Hz    │   (Status channel)     │
│   Peak alpha freq    │   Epoch: -200/+1000ms  │
│   Alpha power        │   Average 16 epochs    │
│                      │   Extract N2/P2        │
└──────────────────────┴────────────────────────┘
         ↓
Results + Figures + group_results.csv
```

---

## Results — sub-001 (Single Subject)

### Preprocessing

| Step | Action |
|------|--------|
| Bad channel | A2 removed (1593 µV — amplitude outlier) |
| Reference | Average reference |
| Bandpass | 1–40 Hz |
| Notch | 50 Hz |

### Alpha Band Analysis

| Metric | Value | Reference |
|--------|-------|-----------|
| Peak alpha frequency | 8.0 Hz | ~10 Hz healthy adults |
| Alpha power | 1.4448 µV²/Hz | — |

### Laser Evoked Potential

| Component | Latency | Amplitude | Reference range |
|-----------|---------|-----------|-----------------|
| N2 | 226.6 ms | 48.42 µV | 150–300 ms |
| P2 | 460.0 ms | 153.42 µV | 300–500 ms |
| NP complex | — | 105.00 µV | 50–150 µV |

---

## Results — Group Analysis (All 26 Subjects)

All 26 subjects processed successfully using event ID 54
(laser stimulus, 16 epochs per subject).

### Group Summary Statistics

| Metric | Mean | SD | Reference range |
|--------|------|----|-----------------|
| N2 Latency | 244.1 ms | ±64.8 ms | 150–300 ms |
| P2 Latency | 381.4 ms | ±99.8 ms | 300–500 ms |
| NP Amplitude | 24.5 µV | ±18.1 µV | 15–30 µV |
| Peak Alpha | 8.5 Hz | ±1.3 Hz | ~10 Hz healthy |

Group N2 and P2 latencies fall within published reference ranges
for healthy adults receiving nociceptive laser stimulation.
Peak alpha (8.5 Hz) is below the typical 10 Hz healthy adult
reference — consistent with the pain-related alpha slowing
reported in the literature.

### Group Distribution Figures

![Group distributions](06_group_distributions.png)

Full individual subject results available in `group_results.csv`.

---

## Comparison With Published Pipeline

The published paper (Xiangyue et al., Scientific Data 2025) used
MATLAB/EEGLAB with a more comprehensive preprocessing pipeline.
Key differences from our independent Python implementation:

| Step | This Analysis | Published Paper |
|------|--------------|-----------------|
| Artifact rejection | Amplitude threshold only | Full ICA (runica) |
| Baseline window | -200ms to 0ms | -1000ms to 0ms |
| Epoch window | -200ms to +1000ms | -1000ms to +2000ms |
| Low-pass filter | 40 Hz | 100 Hz (30 Hz for display) |
| Peak channel | A1 (vertex-adjacent) | Cz (exact vertex) |
| Platform | Python / MNE-Python | MATLAB / EEGLAB |

These differences explain higher variability in our latency estimates.
Our N2 (244ms) and P2 (381ms) group means are directionally consistent
with published LEP literature. Future improvement would apply ICA
artefact rejection and map to exact Cz electrode position.

---

## Output Figures

### Sub-001 Individual Analysis

| Figure | Description |
|--------|-------------|
| 01_raw_psd.png | Raw PSD before any filtering |
| 02_filtering_comparison.png | Before vs after filtering |
| 03_alpha_band.png | All frequency bands + peak alpha 8.0 Hz |
| 04_LEP_waveform.png | Averaged LEP across all channels |
| 05_N2_P2_components.png | N2 and P2 components annotated |

### Group Analysis

| Figure | Description |
|--------|-------------|
| 06_group_distributions.png | N2, P2, alpha, NP distributions — 26 subjects |

### Data Files

| File | Description |
|------|-------------|
| group_results.csv | N2, P2, alpha, NP per subject — all 26 |

---

## Reproducibility

### Requirements

```bash
pip install mne numpy scipy pandas matplotlib jupyter openneuro-py
```

### How To Reproduce — Single Subject (sub-001)

1. Download sub-001 from OpenNeuro:
   👉 openneuro.org/datasets/ds005284

   Files needed:
   - `sub-001_task-26ByBiosemi_eeg.bdf`
   - `sub-001_task-26ByBiosemi_events.tsv`

2. Place in: `data/sub-001/eeg/`

3. Open and run:
   `EEG_Pain_LEP_Analysis_OpenNeuro_ds005284.ipynb`

### How To Reproduce — Group Analysis (All 26 Subjects)

1. Run the download section in the group notebook — it downloads
   all 26 subjects automatically using openneuro-py

2. Open and run:
   `02_Group_Analysis_26_Subjects.ipynb`

3. Results saved automatically to `group_results.csv`

### Environment

| Package | Version |
|---------|---------|
| Python | 3.x |
| MNE-Python | 1.2.3 |
| NumPy | — |
| pandas | — |
| matplotlib | — |
| SciPy | — |
| openneuro-py | 2026.4.1 |

---

## Scientific Context and Motivation

### N2 Component (~244 ms group mean)
Reflects spino-thalamo-cortical transmission of nociceptive signals.
This is the same ascending pain pathway characterised at the spinal
level in my MSc dissertation — specifically, the cold-selective
Calb1-expressing projection neurons in lamina I that transmit to
supraspinal targets including the lateral parabrachial nucleus.

### P2 Component (~381 ms group mean)
Reflects cortical salience and attentional processing of pain.
This is a primary target for TMS and tDCS modulation in translational
pain research — including the CNAP brain neuro-adaptability programme
at Aalborg University, Denmark.

### Alpha Oscillations (8.5 Hz group mean)
Alpha slowing and suppression is a core EEG biomarker of pain
sensitisation. The group mean of 8.5 Hz is below the ~10 Hz healthy
adult reference — consistent with pain-related alpha slowing reported
in the literature (Furman et al., 2020, Pain).

---

## Limitations

- Automated peak detection — boundary effects in some subjects
  (N2 at exactly 150ms or 300ms, P2 at 250ms or 500ms) indicate
  unclear peaks; manual verification recommended for publication
- No ICA artefact rejection applied — published pipeline used
  EEGLAB runica; this would reduce amplitude variability
- Shorter baseline (-200ms) than published pipeline (-1000ms)
- A1 channel used as Cz approximation — exact Cz mapping
  would improve amplitude accuracy
- Single stimulus intensity — this experiment had fixed intensity;
  the full 678-subject dataset varies intensity 2.5J to 4.5J

---

## Next Steps

- [x] Download all 26 subjects from OpenNeuro
- [x] Run group analysis loop — 26/26 subjects processed
- [x] Generate group distribution figures
- [x] Compare results against published reference ranges
- [ ] Apply ICA artefact rejection
- [ ] Map to exact Cz channel position
- [ ] Add ML pain prediction model using group_results.csv features

---

## Code Licence

The analysis code in this repository is released under the
**MIT Licence** — free to use, modify, and distribute with attribution.

The underlying dataset is licensed under **CC0 1.0 Universal**
by the original authors.
See openneuro.org/datasets/ds005284 for terms.

---

## References

**Dataset paper:**
Xiangyue, Z. et al. A comprehensive EEG dataset of laser-evoked
potentials for pain research. *Scientific Data* 12, 1536 (2025).
https://doi.org/10.1038/s41597-025-05900-1

**Dataset:**
Xiangyue, Z. et al. 26 By Biosemi. *OpenNeuro* (2024).
https://doi.org/10.18112/openneuro.ds005284.v1.0.0

**MNE-Python:**
Gramfort, A. et al. MEG and EEG data analysis with MNE-Python.
*Frontiers in Neuroscience* 7, 267 (2013).
https://doi.org/10.3389/fnins.2013.00267

**OpenNeuro platform:**
Markiewicz, C.J. et al. The OpenNeuro resource for sharing of
neuroscience data. *eLife* 10, e71774 (2021).
https://doi.org/10.7554/eLife.71774

**Related dissertation:**
Kongala, S.S.S. Electrophysiological Characterisation of
Calbindin- and Phox2a-Lineage Spinal Neurons in Response to
Somatosensory Stimuli. MSc Dissertation, University of Glasgow
(2025). BBSRC-funded.
github.com/Sai-sasi/Electrophysiological-recordings

---

## Related Repositories

- [Electrophysiological-recordings](https://github.com/Sai-sasi/Electrophysiological-recordings)
  — BBSRC-funded MSc dissertation: R analysis of spinal pain
  circuits (patch-clamp electrophysiology, p=0.033)

- [Bioimaging-Colocalization-Analysis](https://github.com/Sai-sasi/Bioimaging-Colocalization-Analysis)
  — ImageJ/JACoP colocalization analysis,
  University of Glasgow BIOL5261 — Grade A5

- [bioimaging-cell-counting-BBBC001](https://github.com/Sai-sasi/bioimaging-cell-counting-BBBC001)
  — Automated cancer cell counting pipeline,
  CellProfiler 4, Pearson r=0.996 vs published ground truth

---

## Contact

📧 sasisasisekhark@gmail.com
🔗 github.com/Sai-sasi
📍 Glasgow, UK

---

*This project is part of an ongoing portfolio in computational
pain neuroscience, building toward PhD research in translational
neurophysiology.*
