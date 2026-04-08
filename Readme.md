# NeuroViz — EEG Signal Processing & Visualisation

A full-stack, browser-native EEG analysis platform that processes clinical and consumer-grade EEG recordings, computes Frontal Alpha Asymmetry (FAA), and serves a dual-audience dashboard for both clinicians and patients.

---

## Features

- **Hardware-agnostic file support** — accepts `.csv`, `.txt` (OpenBCI), and `.edf` (clinical EDF) formats
- **4-step DSP pipeline** — detrending, 50 Hz notch filter, Butterworth bandpass (0.5–45 Hz), Z-score normalization
- **FAA computation** — Welch PSD estimation, alpha band integration (8–12 Hz), log-ratio asymmetry score
- **Dual-audience dashboard** — Clinician Portal and Patient Wellness View from a single session
- **Artifact detection** — four methods: Threshold, Kurtosis, Muscle (EMG), Ocular (EOG)
- **3D brain model** — interactive Three.js electrode map with anatomically-positioned sensors
- **Raw vs cleaned signal viewer** — per-channel waveform graphs with frequency and amplitude stats

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Python, FastAPI, SciPy, NumPy, MNE, Pandas |
| Frontend | React, Recharts, Three.js, React Three Fiber |
| Styling | Bootstrap 5 |
| Data formats | CSV, OpenBCI TXT, EDF |

---

## Project Structure

```
neuroviz/
├── backend/
│   └── main.py              # FastAPI server — DSP pipeline + feature extraction
├── frontend/
│   └── src/
│       └── components/
│           ├── App.js
│           ├── Dashboard.js         # Main clinician dashboard
│           ├── PatientView.js       # Plain-language wellness view
│           ├── ArtifactDetector.js  # 4-method artifact detection
│           ├── SensorMap.js         # 3D brain model wrapper
│           ├── BrainModel.js        # Three.js GLB brain renderer
│           ├── RawSignalView.js     # Per-channel raw signal graphs
│           ├── TestResults.js       # Clinical stats breakdown
│           ├── TestResultSummary.js # Automated clinical interpretation
│           ├── StatsRow.js          # Expandable stat rows
│           ├── Settings.js          # Pipeline config UI
│           ├── Sidebar.js           # Navigation
│           └── eegData.js           # Electrode position constants
├── data/
│   └── synt.py              # Synthetic EEG dataset generator
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.9+
- Node.js 18+
- npm

### 1. Clone the repository

```bash
git clone https://github.com/Priyankagh24/Neuroviz.git
cd neuroviz
```

### 2. Backend setup

```bash
cd backend
pip install fastapi uvicorn mne scipy numpy pandas
uvicorn main:app --reload
```

Backend runs at `http://127.0.0.1:8000`

### 3. Frontend setup

```bash
cd frontend
npm install
npm start
```

Frontend runs at `http://localhost:3000`

---

## Usage

1. Open `http://localhost:3000` in your browser
2. Upload any supported EEG file (`.csv`, `.txt`, `.edf`)
3. Click **Run Real-time Analysis**
4. Toggle between **Raw** and **Clean Signal** views
5. Use the **Artifact Detector** panel to scan channels
6. Switch between **Clinician** and **Patient** mode in the header

---

## Signal Processing Pipeline

Every uploaded file is automatically processed through 4 steps server-side:

```
Raw EEG → Detrend → 50Hz Notch → Bandpass (0.5–45Hz) → Z-score → Features
```

| Step | Purpose |
|------|---------|
| Detrend | Removes slow baseline drift from electrode movement |
| Notch (50 Hz) | Removes AC power-line interference |
| Bandpass (0.5–45 Hz) | Isolates brain rhythms, removes muscle noise |
| Z-score | Normalizes all hardware units to a single comparable scale |

---

## Frontal Alpha Asymmetry (FAA)

FAA is a validated clinical biomarker for emotional motivation bias (Davidson et al.).

```
FAA = ln(Right frontal alpha) − ln(Left frontal alpha)
    = ln(F4) − ln(F3)   [preferred]
    = ln(F8) − ln(T7)   [fallback if F3/F4 not present]
```

| Score | Interpretation |
|-------|---------------|
| FAA > 0 | Approach motivation — engaged, positive |
| FAA < 0 | Withdrawal state — stressed, anxious |
| FAA ≈ 0 | Neutral / balanced resting state |

---

## Supported Datasets

| File | Type | Description |
|------|------|-------------|
| `noisy_stressed_session.csv` | Synthetic | Generated via `synt.py` with programmed artifacts |
| `s01_ex01_s01.txt` | OpenBCI | Real hardware recording, Subject 01, Session 01 |
| `s01_ex02_s03.txt` | OpenBCI | Real hardware recording, Subject 01, Session 03 |
| `Subject00_1.edf` | Clinical EDF | NeuroCom Professional 3.0, 22-channel clinical recording |

---

## Generating a Synthetic Dataset

```bash
cd data
python synt.py
```

Generates `noisy_stressed_session.csv` with programmed eye blinks, 50 Hz hum, electrode drift, and alpha/beta rhythms for pipeline validation.

---

## Artifact Detection Methods

| Method | Logic | Detects |
|--------|-------|---------|
| Threshold | Flags channels where Z-score max exceeds cutoff | General large-amplitude artifacts |
| Kurtosis | Flags channels where std deviates from 1.0 post Z-score | Heavy-tailed distributions, spikes |
| Muscle (EMG) | Flags channels with dominant frequency > 20 Hz | Muscular contamination |
| Ocular (EOG) | Flags channels with dominant frequency < 4 Hz | Eye blinks, slow eye movements |

---

## License

MIT License — free to use, modify, and distribute.

---

## Author

Built as a mini project in 6th sem.