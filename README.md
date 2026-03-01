![DualLoc](Logo.png)


DualLoc is a multi-label protein subcellular localization predictor using full-parameter fine-tuning of dual Protein Language Models (PLMs). It predicts localization across **10 subcellular compartments** and **9 sorting signal types** simultaneously. 

📄 **Paper**: DualLoc: Full-parameter fine-tuning of dual transformer models for protein subcellular localization prediction

---

## Overview

DualLoc integrates two distinct PLM encoders trained simultaneously:
- **Encoder 1**: Initialized with pretrained weights (ProtBERT / ESM-2 / ProtT5) to leverage broad evolutionary context
- **Encoder 2**: Randomly initialized to specialize in task-specific localization patterns

Both encoders are augmented with multi-head attention, Gaussian smoothing, dropout layers, and fully connected classification layers. DualLoc also supports hierarchical prediction, where localization features feed into a secondary pathway for sorting signal classification.

---

## Quick Install

> We suggest installing all packages using conda (both Miniconda and [Anaconda](https://anaconda.org/) are supported).

### 1. First-time Setup

```bash
# Create conda environment
conda create -n DualLoc python=3.10 -y
conda activate DualLoc

# Install PyTorch with CUDA 11.8
conda install pytorch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 pytorch-cuda=11.8 -c pytorch -c nvidia

# Install required packages
pip install transformers sentencepiece protobuf
pip install scikit-learn pandas numpy matplotlib seaborn
pip install umap-learn tqdm

# Clone repository
git clone https://github.com/NTOUBiomedicalAILAB/DualLoc.git
cd DualLoc/
```

### 2. Subsequent Usage

```bash
conda activate DualLoc
```

---

## Usage

### Run the Complete Pipeline

```bash
python src/main.py
```

This executes the full pipeline:
1. Load and preprocess `signal_sorting.csv`
2. Split data (70% train / 30% test)
3. Initialize DualLoc-ProtT5 model
4. Train for 10 epochs with checkpoint saving
5. Evaluate and output metrics
6. Run single sequence prediction demo

### Single Sequence Prediction

Modify the `sample_text` variable in `main.py`:

```python
sample_text = "MYWSNQITRRLGERVQGFMSGISPQQMGE..."
classifier.predict_raw_text(trained_model, sample_text, target_list)
```

Output example:
```
Predicted localizations:
 - SP
 - TM
```

---

## Input Data Format

### `signal_sorting.csv`

Place the CSV file under `data/signal_sorting.csv`. The file must contain the following **10 columns**:

```csv
Sequence,CH,GPI,MT,NES,NLS,PTS,SP,TH,TM
MYWSNQITRRLGERV...,0,1,0,0,0,0,1,0,0
MAAKLVFASSGQKFQ...,0,0,1,0,1,0,0,0,0
```

| Column | Description |
|--------|-------------|
| `Sequence` | Amino acid sequence (string) |
| `CH` | Chloroplast transit peptide |
| `GPI` | GPI-anchor signal |
| `MT` | Mitochondrial targeting signal |
| `NES` | Nuclear Export Signal |
| `NLS` | Nuclear Localization Signal |
| `PTS` | Peroxisomal Targeting Signal |
| `SP` | Signal Peptide |
| `TH` | Thylakoidal transfer peptide |
| `TM` | Transmembrane segment |

- Label values: `0` or `1` (multi-label, multiple columns can be `1` simultaneously)
- Maximum sequence length: 512 tokens
- Default sample size: 1,868 sequences

---


## Project Structure

```
DualLoc/
├── src/
│   ├── config.py       # Hyperparameters and path settings
│   ├── model.py        # Dual T5 encoder architecture
│   ├── dataset.py      # Tokenizer and DataLoader
│   ├── trainer.py      # Training loop with checkpointing
│   └── main.py         # Main pipeline entry point
├── data/
│   └── signal_sorting.csv   # Input data (not included, see below)
└── README.md
```

---

## Data Availability

The sorting signal dataset was compiled from annotations in DeepLoc 2.0 and Swiss-Prot, comprising 1,868 sequences across 9 sorting signal classes.

The subcellular localization training data is derived from manually reviewed Swiss-Prot entries (UniProt release 2021-03-23), containing 28,303 unique sequences across 10 compartments. Independent validation uses Human Protein Atlas (HPA) data with 1,717 non-redundant sequences.

> 🔒 The dataset files are not included in this repository. Please refer to [DeepLoc 2.0](https://services.healthtech.dtu.dk/services/DeepLoc-2.0/) to obtain and format the data according to the Input Data Format section above.

---

## Configuration

Key parameters in `src/config.py`:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `model_name` | `Rostlab/prot_t5_xl_uniref50` | Pretrained PLM (auto-downloaded from HuggingFace) |
| `EPOCHS` | 10 | Number of training epochs |
| `LEARNING_RATE` | 2e-4 | AdamW learning rate |
| `MAX_LEN` | 512 | Maximum sequence length |
| `dropout_rate` | 0.3 | Dropout rate |
| `THRESHOLD` | 0.5 | Prediction threshold (multi-label) |
| `num_labels` | 9 | Number of output classes |



---
