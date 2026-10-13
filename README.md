# Grin2a-mpfc-laminar-dynamics
Analysis pipeline for multielectrode recordings of Grin2a mutant mPFC: spike detection, laminar connectivity, network motifs and genotype classification.


## Overview

Spontaneous activity was recorded from acute prelimbic mPFC slices on 60-electrode multielectrode arrays (Multi Channel Systems, 20 kHz). Electrodes were assigned to layer 2/3 (PrL23) or layer 5 (PrL5). This repository contains the full analysis pipeline, from raw `.h5` recordings to the statistics and figures reported in the paper.

**Unit of analysis.** All slices recorded on the same date come from the same animal. Slice values are averaged within each animal before statistical testing, and all cross-validation is grouped by animal, so no animal contributes data to both training and testing.

## Repository structure

```
grin2a-mpfc-laminar-dynamics/
├── 01_spike_detection/          # per-slice spike/event detection, validation and sampling sufficiency
├── 02_group_comparison/         # genotype comparisons of event rate, spike shape and sufficiency (animal & slice level)
├── 03_window_sufficiency/       # split-half consistency and duration convergence of motif estimates
├── 04_waveforms_rasters/        # mean spike waveforms per genotype and representative rasters
├── 05_coherence_granger/        # coherence and Granger causality statistics (GBO, HFO)
├── 06_motif_analysis/           # surrogate-normalized three-node motif statistics
├── 07_machine_learning/         # GLM, PCA, nested grouped cross-validation, permutation tests
├── 08_deep_learning/            # leave-one-animal-out deep-learning classification
├── requirements.txt
└── README.md
```

*Adjust folder and notebook names to match the files you upload.*

## Pipeline

| Step | What it does | Main outputs |
|---|---|---|
| **1. Spike detection** | 300–3000 Hz zero-phase Butterworth filter at native 20 kHz; percentile threshold on signed voltage; 1-ms dead time; MUA classification; population-event detection; sampling-sufficiency metrics (expected chance coincidences, triplet adequacy, split-half reproducibility) | `*_per_channel_summary.csv`, `*_spike_times.npz`, `*_median_waveforms.npz`, `*_sufficiency_summary.json`, `*_detection_parameters.json` |
| **2. Group comparison** | Event number, rate, spike width and sufficiency compared across genotypes; one-way ANOVA + Tukey at animal and slice level | `animal_level_raw.csv`, `slice_level_raw.csv`, `anova_results*.csv`, figures |
| **3. Window sufficiency** | Motif z-scores in non-overlapping halves and increasing durations (1–4 min) | `consistency_halves_*.csv`, `convergence_duration_*.csv`, figures |
| **4. Waveforms & rasters** | Mean waveform per genotype and layer (mean ± SEM across animals); representative rasters with population rate | `mean_waveform_*.png/pdf`, `raster_representative.png/pdf` |
| **5. Coherence & Granger** | Per-band (GBO, HFO) one-way ANOVA per layer, Benjamini–Hochberg across layers, Tukey post hoc | `stats_animal_level_*.csv`, figures |
| **6. Motifs** | Surrogate-normalized motif z-scores, signed-log transformed; one-way ANOVA per signal type × layer; motif-level Welch tests with FDR | `anova_<level>.csv`, `motif_level_FDR_<level>.csv`, figures |
| **7. Machine learning** | GLM (group vs rest); PCA; seven classifiers with nested cross-validation grouped by animal; animal-level label permutation (FDR and selection-corrected p-values); confusion matrices | `observed_results.csv`, `permutation_null.csv`, figures |
| **8. Deep learning** | Band-power features (7 bands × 59 channels, 1-s windows); RNN, BiRNN, GRU, CNN and CNN-LSTM; leave-one-animal-out cross-validation with animal-level validation and majority vote | `per_animal_per_model_LOGO_results.csv`, figures |

## Requirements

Python ≥ 3.9. Install dependencies with:

```bash
pip install -r requirements.txt
```

Main packages: `numpy`, `scipy`, `pandas`, `matplotlib`, `seaborn`, `h5py`, `scikit-learn`, `statsmodels`, `statannotations`, `networkx`, `tensorly`, `xgboost`, `tensorflow`.

## Usage

1. Set the data and output paths in the configuration cell at the top of each notebook or script.
2. Run step 1 on every recording. Outputs are written to `Output/<GENOTYPE>/...`.
3. Run steps 2–8, which read the step-1 outputs (or the connectivity/motif result files) and write tables and figures to their own output folders.

Each analysis records its parameters and software versions (for example `detection_parameters.json`, `analysis_parameters.json`) for reproducibility.

## Data

Raw recordings are not included in this repository because of their size. Data are available from the corresponding author.

## Figure conventions

Genotype colours: WT `#000000`, HET `#888888`, KO `#FF0000`. Layers: PrL23, PrL5, PrL23-5.

