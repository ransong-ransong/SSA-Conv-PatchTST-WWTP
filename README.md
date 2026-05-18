# SSA-Conv-PatchTST-WWTP

This repository contains the data, code, configurations, and result summaries for the manuscript on multi-horizon aeration-volume forecasting in wastewater treatment plants using SSA-Conv-PatchTST.

## Repository Layout

```text
SSA-Conv-PatchTST-WWTP/
├── README.md
├── requirements.txt
├── environment.yml
├── LICENSE
├── data/
│   ├── README_data.md
│   ├── raw/
│   ├── processed/
│   ├── splits/
│   └── external/
├── src/
│   ├── preprocess.py
│   ├── train.py
│   ├── train_ssa_conv_patchtst.py
│   ├── train_baselines.py
│   ├── train_external_validation.py
│   ├── evaluate.py
│   └── models/
├── configs/
│   ├── ssa_conv_patchtst.yaml
│   └── baselines.yaml
├── results/
│   ├── main_results.csv
│   ├── external_validation.csv
│   ├── efficiency_results.csv
│   └── generated/
└── scripts/
    ├── run_main_experiment.sh
    ├── run_external_validation.sh
    ├── run_main_experiment.ps1
    └── run_external_validation.ps1
```

## Data

The main WWTP dataset is provided in two forms:

- `data/raw/device_data_wide_final_1min1.csv`: original wide-format process data.
- `data/processed/device_data_wide_final_1min1_filtered_ffill.csv`: filtered and forward-filled dataset used by the experiments.

The external validation dataset should be obtained from the DataFountain competition "Process Control of a Water Purification Plant: Aeration Volume Prediction" and placed at:

```text
data/external/train_dataset.csv
```

See `data/README_data.md` for details.

## Environment

Install Python dependencies:

```bash
pip install -r requirements.txt
```

The scripts use official TSLib model implementations. Set the local TSLib path before running:

```bash
export TSLIB_OFFICIAL_ROOT=/path/to/TSLib_official
```

On PowerShell:

```powershell
$env:TSLIB_OFFICIAL_ROOT="C:\path\to\TSLib_official"
```

## Run the Proposed Model

```bash
python src/train.py --mode ssa --model-name SSA-Conv-PatchTST --decomp-method ssa --ssa-window 120 --ssa-components 4 --random-state 7
```

Equivalent direct call:

```bash
python src/train_ssa_conv_patchtst.py --model-name SSA-Conv-PatchTST --decomp-method ssa --ssa-window 120 --ssa-components 4 --random-state 7
```

## Run Baselines

```bash
python src/train.py --mode baseline --model-name PatchTST --random-state 7
python src/train.py --mode baseline --model-name TimeXer --random-state 7
python src/train.py --mode baseline --model-name iTransformer --random-state 7
python src/train.py --mode baseline --model-name DLinear --random-state 7
```

The provided baseline script also supports TiDE, SCINet, Crossformer, GRU, and LSTM.

## Run External Validation

```bash
python src/train.py --mode external --model-name SSA-Conv-PatchTST --target-label label1 --decomp-method ssa --random-state 7
python src/train.py --mode external --model-name SSA-Conv-PatchTST --target-label label2 --decomp-method ssa --random-state 7
```

## Result Files

- `results/main_results.csv`: five-seed main-dataset performance summary.
- `results/external_validation.csv`: cross-dataset external validation summary.
- `results/efficiency_results.csv`: computational efficiency summary.
- `results/generated/`: default output folder for newly generated experiment files.

## Notes

The SSA feature-enhancement branch is implemented as an offline research workflow. SSA-derived features are generated before supervised window construction, and chronological splitting plus training-only normalization are then applied in the modelling pipeline.
