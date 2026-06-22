# HGCAL — YOLO Energy Regression: Full Documentation

## Overview

This document covers the complete end-to-end workflow for the HGCAL particle energy regression project using a customised YOLOv5 model. It includes:

- Signal event generation with Geant4
- Cell-wise digitisation (Step-1 → Step-2)
- Pileup event generation via Pythia8 and Geant4
- Signal + pileup merging
- YOLO image preprocessing
- Model training (single-GPU and multi-GPU)
- Inference and testing

**Main simulation repository:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL  
**Model repository:** https://github.com/Sid2690/HGCAL_YOLOV5_Linear  
**Model repository (custom channels):** https://github.com/Sid2690/HGCAL_YOLOV5_Custom

---

## Repository Structure

```text
HGCAL/
├── Codes/
│   ├── Segmentation/           ← Cell-wise segmentation (Step-1 → Step-2)
│   ├── PileupUtils/            ← Pythia ROOT → text conversion
│   ├── SignalToPileupAdittion/ ← Merging signal + pileup Step-2 files
│   └── YOLO/                   ← Image generation scripts for ML
├── Pileup_Simulation/          ← Geant4 pileup simulation
└── Single_Particle_Simulation/ ← Geant4 signal simulation
```

---

## Data Directory Structure

**Base path:** `/persistent/data1/ms21080/Data`  
**Total storage:** ~12 TB

```text
Data/
├── Fixed_pT_Step1_Data
├── Fixed_pT_Step2_Data
├── Variable_pT_Step1_Data
├── Variable_pT_Step2_Data
├── PileUp_Pythia_Data
├── Pileup_Step1_Data
├── Pileup_Step2_Data
├── Signal_PileUp_Data
├── Unwanted
└── README.md
```

### Fixed_pT_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Fixed_pT_Step1_Data`

Contains Geant4 Step-1 simulation output ROOT files for the fixed pT–η setup. These files contain the SimHit information directly produced by Geant4 before digitisation.

| Directory | Size |
|---|---|
| Electron_Step1 | 907 G |
| MuonM_Step1 | 3.8 G |
| Photon_Step1 | 888 G |
| Positron_Step1 | 907 G |

Example files:
```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step1.root
Electron_nPU_000_Pt_100_Eta_260_Events_2K_Set01_Step1.root
```

### Variable_pT_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Variable_pT_Step1_Data`

Contains Geant4 Step-1 simulation outputs generated using the variable pT–η configuration. Unlike the fixed-pT dataset, these samples cover a continuous range of pT and η values.

| Directory | Size |
|---|---|
| Electron_Step1 | 171 G |
| MuonM_Step1 | 725 M |
| Photon_Step1 | 164 G |
| Positron_Step1 | 170 G |

Example files:
```text
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set01_Step1.root
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set02_Step1.root
```

### Fixed_pT_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Fixed_pT_Step2_Data`

Contains the digitised version of the fixed-pT Step-1 datasets. Step-1 ROOT files are processed through the cell-wise segmentation code, converting Geant4 SimHits into detector cell-level DigiHits.

| Directory | Size |
|---|---|
| Electron_Step2 | 15 G |
| MuonM_Step2 | 1.6 G |
| Photon_Step2 | 13 G |
| Positron_Step2 | 14 G |

Example files:
```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step2.root
Electron_nPU_000_Pt_025_Eta_185_Events_2K_Set01_Step2.root
```

### Variable_pT_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Variable_pT_Step2_Data`

Contains digitised detector outputs corresponding to the variable pT–η Step-1 datasets.

| Directory | Size |
|---|---|
| Electron_Var_Step2 | 2.4 G |
| MuonM_Var_Step2 | 64 M |
| Photon_Var_Step2 | 2.1 G |
| Positron_Var_Step2 | 2.4 G |

Example files:
```text
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set01_Step2.root
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set02_Step2.root
```

### PileUp_Pythia_Data

**Path:** `/persistent/data1/ms21080/Data/PileUp_Pythia_Data`

Contains pileup events generated using Pythia8, serving as the starting point for the pileup simulation workflow.

| File | Size | Description |
|---|---|---|
| Pythia8_PU_Events_20K_v0.root | 1.8 G | Hard-QCD interaction sample |
| Pythia8_PU_Events_20K_v1.root | 72 M | Soft-collision pileup sample |
| Pythia8_PU_Events_20K_v2.root | 98 M | Updated soft-collision pileup sample (latest) |
| Final_Bharat/ | — | Final pileup files used as Geant4 input |

**Final_Bharat subdirectory** — `/persistent/data1/ms21080/Data/PileUp_Pythia_Data/Final_Bharat`

| File | Size | Description |
|---|---|---|
| Bharat_final_root.root | 72 M | Final pileup ROOT file used throughout this project |
| rootToText.C | 4.0 K | Converts ROOT file to Geant4-compatible text format |
| generated_data.txt | 11 M | Text file used as input for Geant4 pileup simulation |

### Pileup_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Pileup_Step1_Data`

Contains Geant4 Step-1 simulation outputs generated from the Pythia pileup events. Each version corresponds directly to the matching Pythia sample (v0, v1, v2).

| File | Size |
|---|---|
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v0.root | 68 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v1.root | 8.1 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v2.root | 11 G |

### Pileup_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Pileup_Step2_Data`

Digitised detector-level outputs corresponding to the pileup Step-1 datasets.

| File | Size |
|---|---|
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v0.root | 5.7 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v1.root | 856 M |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v2.root | 1.1 G |

### Signal_PileUp_Data

**Path:** `/persistent/data1/ms21080/Data/Signal_PileUp_Data`

Contains the final merged datasets (signal + pileup). The `nPU` field in filenames indicates the number of pileup interactions merged with the signal.

| Directory / File | Size |
|---|---|
| Electron_Fix_Pileup | 1.4 T |
| Electron_Var_Pileup | 295 G |
| MuonM_Fix_Pileup | 1.3 T |
| MuonM_Var_Pileup | 186 G |
| Photon_Fix_Pileup | 1.4 T |
| Photon_Var_Pileup | 212 G |
| Positron_Fix_Pileup | 1.4 T |
| Positron_Var_Pileup | 212 G |
| job/ | 1.8 T |
| job.sh | 4.0 K |

Example files:
```text
Electron_nPU_035_Pt_025_Eta_170_Events_2K_Set01_Step2.root   ← 35 pileup interactions
Electron_nPU_100_Pt_025_Eta_245_Events_2K_Set01_Step2.root   ← 100 pileup interactions
```

The `job/` subdirectory contains submission scripts and configuration files for the signal–pileup merging production runs. It is not required for downstream analysis.

---

## Overall Pipeline

```
Pythia8 Pileup Generation
           ↓
  Bharat_final_root.root
           ↓
       rootToText.C
           ↓
   generated_data.txt          Signal Geant4 Simulation
           ↓                              ↓
  Pileup Step-1 ROOT             Signal Step-1 ROOT
           ↓                              ↓
     Digitisation                   Digitisation
           ↓                              ↓
  Pileup Step-2 ROOT             Signal Step-2 ROOT
                    ↘           ↙
              Signal + Pileup Merging
                         ↓
            Signal_PileUp Step-2 ROOT
                         ↓
           YOLO Image Preprocessing
                         ↓
         images/ + labels/ + energy/
                         ↓
              YOLOv5 Training
                         ↓
                     best.pt
                         ↓
              detect.py Inference
                         ↓
           Predicted Labels for Analysis
```

---

## Step 1 — Signal Event Generation

**Directory:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Single_Particle_Simulation

This step uses the Geant4-based simulation to produce signal events (electrons, photons, positrons, muons, etc.).

### 1.1 Configure the Particle Gun

Open `generator.cc` and set the following parameters according to the sample you want to produce:

- Particle type / ID
- η (eta) range or fixed value
- φ (phi)
- pT (transverse momentum) — fixed value or range
- Any other generator-level parameters

### 1.2 Configure the Run

Navigate to `Single_Particle_Simulation/build/` and open `run.mac`. Set:

- Number of events to generate
- Random seed (update if generating multiple sets to avoid overlap)

### 1.3 Run the Simulation

Make sure Geant4 is installed and the environment is properly configured, then submit:

```bash
qsub run.sh
```

### 1.4 Output

A successful run produces a Step-1 ROOT file:

```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step1.root
```

**Filename convention:**

| Field | Meaning |
|---|---|
| `Electron` | Particle type |
| `nPU_000` | Number of pileup interactions (zero at this stage) |
| `Pt_025` | Transverse momentum (GeV) |
| `Eta_170` | η bin |
| `Events_2K` | 2000 events |
| `Set01` | Dataset set index |
| `Step1` | Geant4 SimHit output (before digitisation) |

These Step-1 files contain raw detector SimHits.

---

## Step 2 — Cell-wise Segmentation (Step-1 → Step-2)

**Directory:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Codes/Segmentation

This step converts Geant4 SimHits from Step-1 files into digitised detector cell-level DigiHits (Step-2 files).

```text
Segmentation/
├── cellwise_segmentation.C   ← Main segmentation macro
├── automation.C              ← Batch processing for many files
└── job.sh                    ← PBS cluster submission script
```

### 2.1 Single File

Run `cellwise_segmentation.C` directly in ROOT, pointing it at your Step-1 file.

### 2.2 Batch Processing

For large numbers of Step-1 files, use `automation.C` together with `job.sh` to submit parallel jobs on the cluster:

```bash
qsub job.sh
```

### 2.3 Input / Output

```text
Input:  Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step1.root
Output: Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step2.root
```

Step-2 files contain the digitised detector response and serve as input to all subsequent stages.

---

## Step 3 — Pileup Event Generation

**Directory:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Pileup_Simulation

Pileup events are generated from a Pythia8 ROOT file. The workflow requires first converting that ROOT file into a plain text format that Geant4 can read as a particle gun input.

### 3.1 Convert Pythia ROOT to Text

**Script:** https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/PileupUtils/01_rootToText.C

1. Open `01_rootToText.C`
2. Modify the code if needed to match the structure of your Pythia ROOT file
3. Set the path to the input Pythia ROOT file inside the macro (the final file used in this project is `Bharat_final_root.root`)
4. Run the ROOT macro

```bash
root -l -b -q 01_rootToText.C
```

**Output:** `generated_data.txt`

### 3.2 Run the Pileup Geant4 Simulation

1. Copy `generated_data.txt` into the `Pileup_Simulation/build/` directory
2. If needed, modify the output ROOT file name in `run.cc`
3. Modify `run.mac` to set the number of events and update the seed if running multiple versions
4. Submit:

```bash
qsub run.sh
```

**Output:**
```text
PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1.root
```

This is the pileup Step-1 file. The naming indicates particles with pT > 0.3 GeV, η in the range 1.5–3.1, and 20K events.

### 3.3 Digitise the Pileup Step-1 File

Run the pileup Step-1 file through the same segmentation workflow as signal events:

**Directory:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Codes/Segmentation

```text
Input:  PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1.root
Output: PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2.root
```

---

## Step 4 — Merging Signal and Pileup

**Script:** https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/SignalToPileupAdittion/addPileupToSignal_03.C  
**Directory:** https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Codes/SignalToPileupAdittion

Once both signal and pileup Step-2 files are available, they are combined by overlaying `nPU` randomly sampled pileup interactions onto each signal event.

### 4.1 Required Input Files

```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step2.root   ← signal
PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2.root             ← pileup
```

### 4.2 Configure the Macro

Inside `addPileupToSignal_03.C`, set the function parameters:

```cpp
void addPileupToSignal(
    const char* signalFile = "Electron_Pt_025_Eta_170_Events_2K_PU_000_Set01_Step2.root",
    const char* pileupFile = "PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2.root",
    const char* outputFile = "Electron_nPU_35_Pt_025_Eta_170_Events_2K_Set01_Step2.root",
    int nPU = 35,
    int classLabel = 0)
```

| Parameter | Description |
|---|---|
| `signalFile` | Path to the signal Step-2 ROOT file |
| `pileupFile` | Path to the pileup Step-2 ROOT file |
| `outputFile` | Name of the merged output file |
| `nPU` | Number of pileup interactions to overlay per event |
| `classLabel` | Integer class identifier (e.g. 0 for electron, different value for muon) |

### 4.3 Batch Automation

For large-scale production, use `electron_var_pileup_automation.C` and `job.sh` in the same directory to submit parallel merging jobs.

### 4.4 Output

```text
Electron_nPU_070_Pt_025_Eta_170_Events_2K_Set01_Step2.root
```

The non-zero `nPU` Step-2 files are the final input to the ML preprocessing pipeline.

---

## Step 5 — Preparing Data for YOLO (Image Generation)

**Core script:** https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/process_root.py

This script reads the merged Signal+PileUp Step-2 ROOT files and produces three outputs per event:

- A 736×736×16 float32 `.npy` image (47 detector layers collapsed into 16 channels by summing every 3 layers)
- A YOLO-format bounding box `.txt` label file
- An energy `.txt` file containing the true generator-level energy in MeV

Events are automatically shuffled and split into `train/`, `val/`, and `test/` subdirectories.

### 5.1 Output Directory Structure

After running the preprocessing, the output directory will contain:

```text
<OUT_DIR>/
├── images/
│   ├── train/    ← .npy image arrays (float32, 736×736×16)
│   ├── val/
│   └── test/
├── labels/
│   ├── train/    ← YOLO format: class cx cy width height
│   ├── val/
│   └── test/
└── energy/
    ├── train/    ← true energy in MeV (one value per file)
    ├── val/
    └── test/
```

### 5.2 Key Parameters in `process_root.py`

| Parameter | Description | Options / Example |
|---|---|---|
| `box_size` | Bounding box size based on energy containment fraction | `'s'` = 90%, `'n'` = 95%, `'l'` = 98% |
| `fixed_box` | Use a fixed box size instead of per-event containment fractions | `True` or `False` |
| `fixed_w`, `fixed_h` | Fixed box dimensions in normalised coordinates (used when `fixed_box=True`) | `0.034249`, `0.107281` |
| `val_fraction` | Fraction of events assigned to the validation split | `0.1` |
| `test_fraction` | Fraction of events assigned to the test split | `0.0` for training jobs |
| `n_events` | Number of events to process (`-1` processes all events) | `-1` |
| `seed` | Random seed for the train/val/test shuffle | `42` |

### 5.3 Generating Training Data

**Submission script:** https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/submit_training.sh

This script submits one PBS job per dataset set (Set01–Set05). Each job calls `process_root_file()` on one ROOT file and assigns 10% of events to validation.

Edit the path variables at the top of the script before running:

```bash
JOBDIR="/persistent/data1/ms21080/Regression/Data/electron"
INDIR="/persistent/data1/ms21080/Data/Signal_PileUp_Data/Electron_Var_Pileup"
OUTBASE="/persistent/data1/ms21080/Regression/Data/Electron/100"
```

The processing options inside each submitted job:

```python
process_root_file(
    in_file=IN_FILE,
    prefix="ele_var_set${SET}",
    start_index=0,
    n_events=-1,
    val_fraction=0.1,    # 10% of events go to val/
    test_fraction=0.0,   # no test split for training jobs
    box_size='l',        # 98% energy containment box
    base_dir=OUT_DIR,
    fixed_box=True,
    seed=41
)
```

Submit:

```bash
bash submit_training.sh
```

### 5.4 Generating Testing Data

**Submission script:** https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/submit_testing.sh

This script loops over multiple pT values (`025, 050, 100, 150, 200`) and submits one job per fixed pT–η point, using fixed-pileup signal files.

Edit the path variables at the top:

```bash
JOBDIR="/persistent/data1/ms21080/Regression/Data/Fix/any"
INDIR="/persistent/data1/ms21080/Data/Signal_PileUp_Data/Electron_Fix_Pileup"
PU=200     # number of pileup interactions
ETA=220    # eta bin
```

The processing options inside each job:

```python
process_root_file(
    in_file=IN_FILE,
    prefix="ele_PU${PU}_PT${PT}_ETA${ETA}",
    start_index=0,
    n_events=-1,
    val_fraction=0.0,    # all events go to train/ folder
    test_fraction=0.0,
    box_size='l',
    base_dir=OUT_DIR,
    fixed_box=True,
    seed=42
)
```

Submit:

```bash
bash submit_testing.sh
```

> **Note:** For testing jobs, both `val_fraction` and `test_fraction` are set to `0.0`, so all events land in the `train/` subfolder. When running `detect.py` later, point `--source` at `.../images/train/`.

---

## Step 6 — YOLO Model Setup

**Repository (16-channel):** https://github.com/Sid2690/HGCAL_YOLOV5_Linear  
**Repository (custom channels):** https://github.com/Sid2690/HGCAL_YOLOV5_Custom

Clone the appropriate repository and install dependencies:

```bash
git clone https://github.com/Sid2690/HGCAL_YOLOV5_Linear.git
cd HGCAL_YOLOV5_Linear
pip install -r requirements.txt
```

> The `HGCAL_YOLOV5_Linear` repository is configured for **16-channel** input. If you change the number of channels, use `HGCAL_YOLOV5_Custom` instead, which supports different channel counts via the YAML config without breaking the setup.

### 6.1 Place the Preprocessed Data

Copy the three output directories from the preprocessing step into the model's `data/` folder:

```bash
cp -r <OUT_DIR>/images  HGCAL_YOLOV5_Linear/data/
cp -r <OUT_DIR>/labels  HGCAL_YOLOV5_Linear/data/
cp -r <OUT_DIR>/energy  HGCAL_YOLOV5_Linear/data/
```

### 6.2 Dataset Configuration

**File:** `data/muon47m.yaml` (or create your own)

```yaml
path: ./data
train: images/train
val: images/val

nc: 1           # number of classes
names: ['electron']   # class names — update to match your particle
ch: 16          # number of input channels — must match the preprocessed data
```

| Parameter | Description |
|---|---|
| `nc` | Number of particle classes |
| `names` | List of class names |
| `ch` | Number of input image channels — must match the `.npy` array depth |

### 6.3 Hyperparameter Configuration

**File:** https://github.com/Sid2690/HGCAL_YOLOV5_Linear/blob/main/data/hyps/hyp_muon47.yaml

The parameters most commonly adjusted:

```yaml
lr0: 0.001           # initial learning rate
lrf: 0.01            # final learning rate fraction
momentum: 0.937
weight_decay: 0.0005
warmup_epochs: 5.0
warmup_momentum: 0.8
warmup_bias_lr: 0.1
box: 0.05            # bounding box loss weight
cls: 0.5             # classification loss weight
energy: 0.1          # energy regression loss weight
cls_pw: 2.0
obj: 1.0
obj_pw: 1.0
iou_t: 0.2
anchor_t: 4.0
```

> The remaining parameters in the file control data augmentation. These should generally not be modified, as physics-based image augmentations can distort the detector response information.

### 6.4 Model Architecture Configuration

**File:** https://github.com/Sid2690/HGCAL_YOLOV5_Linear/blob/main/models/yolo47m.yaml

```yaml
nc: 1
ch: 16
depth_multiple: 0.67
width_multiple: 0.75

anchors:
  - [3,3, 5,5, 8,10]
  - [10,13, 16,30, 33,23]
  - [30,61, 62,45, 59,119]
```

Adjustable parameters: `nc`, `ch`, `depth_multiple`, `width_multiple`, `anchors`. Full architectural modifications can also be made through this file if needed.

---

## Step 7 — HPC Setup

Before training, ensure the HPC GPU environment is set up correctly. Refer to the IISER HPC GPU guide: https://iisermhpc.pages.dev/gpu_.html

### 7.1 Installing TMUX

TMUX keeps training sessions alive after terminal disconnection. Since it may not be available system-wide, install it locally:

**Install libevent (dependency):**

```bash
cd ~
wget https://github.com/libevent/libevent/releases/download/release-2.1.12-stable/libevent-2.1.12-stable.tar.gz
tar -xzf libevent-2.1.12-stable.tar.gz
cd ~/libevent-2.1.12-stable
./configure --prefix=$HOME/local
make -j4
make install
```

**Install TMUX:**

```bash
cd ~
wget https://github.com/tmux/tmux/releases/download/3.4/tmux-3.4.tar.gz
tar -xzf tmux-3.4.tar.gz
cd tmux-3.4
CPPFLAGS="-I$HOME/local/include" \
LDFLAGS="-L$HOME/local/lib" \
PKG_CONFIG_PATH="$HOME/local/lib/pkgconfig" \
./configure --prefix=$HOME/local
make -j4
make install
```

**Add to PATH and library path permanently:**

```bash
echo 'export PATH=$HOME/local/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=$HOME/local/lib:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

**Verify installation:**

```bash
tmux -V
# Expected output: tmux 3.4
```

**Start a persistent session before training:**

```bash
tmux new -s training
```

To detach from the session without stopping training: `Ctrl+B` then `D`. To reattach: `tmux attach -t training`.

### 7.2 Environment Activation

Run this block at the start of every training or inference session:

```bash
cd /persistent/data1/ms21080/Regression/Data/Model_Nominal
conda activate yolov5
unset LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.6/targets/x86_64-linux/lib:/usr/local/cuda-11.6/lib64
```

> Check `requirements.txt` in the model directory to confirm the correct conda environment name and package versions.

---

## Step 8 — Training

### 8.1 Create Dataset Cache Files (Required Before DDP)

Before launching multi-GPU (DDP) training, run a short single-GPU job first. This causes the dataset loader to create `.cache` files, which are required for DDP to work correctly. Once the cache files appear on disk, you can terminate this run early.

```bash
python train.py \
    --data data/muon47m.yaml \
    --cfg models/yolo47m.yaml \
    --hyp data/hyps/hyp_muon47.yaml \
    --weights '' \
    --img 736 \
    --batch-size 8 \
    --epochs 30 \
    --device 1 \
    --workers 2
```

> `--weights ''` means training from scratch. To fine-tune from a previous checkpoint, provide its path instead.

### 8.2 Single-GPU Training

Use this when only one GPU is available, or for quick experiments:

```bash
python train.py \
    --data data/muon47m.yaml \
    --cfg models/yolo47m.yaml \
    --hyp data/hyps/hyp_muon47.yaml \
    --weights '' \
    --img 736 \
    --batch-size 8 \
    --epochs 30 \
    --device 0 \
    --workers 2
```

### 8.3 Multi-GPU Training (DDP — Recommended)

Use PyTorch distributed training when multiple GPUs are available. This is the recommended approach for full training runs.

```bash
export NCCL_TIMEOUT=1800
export TORCH_NCCL_BLOCKING_WAIT=1
export NCCL_DEBUG=INFO

python -m torch.distributed.run --nproc_per_node 4 train.py \
    --data data/muon47m.yaml \
    --cfg models/yolo47m.yaml \
    --hyp data/hyps/hyp_muon47.yaml \
    --weights '/persistent/data1/ms21080/Regression/Data/Test/100/runs/train/v1/weights/best.pt' \
    --img 736 \
    --batch-size 8 \
    --epochs 100 \
    --device 0,1,2,3 \
    --workers 2 \
    --optimizer Adam \
    --cos-lr \
    --project /persistent/data1/ms21080/Regression/Data/Test/035/runs/train/ \
    --name v1
```

**Training argument reference:**

| Argument | Description |
|---|---|
| `--nproc_per_node 4` | Number of GPUs to use |
| `--data` | Dataset YAML configuration file |
| `--cfg` | Model architecture configuration file |
| `--hyp` | Hyperparameter configuration file |
| `--weights` | Path to a pretrained `.pt` file to fine-tune from; `''` to train from scratch |
| `--img 736` | Input image size (736×736 pixels) |
| `--batch-size` | Batch size per GPU |
| `--epochs` | Total number of training epochs |
| `--device` | Comma-separated GPU indices (e.g. `0,1,2,3`) |
| `--optimizer Adam` | Optimiser choice |
| `--cos-lr` | Enable cosine learning-rate decay scheduling |
| `--project` | Root directory where run outputs are saved |
| `--name v1` | Run name; outputs saved to `<project>/v1/` |
| `--workers` | Number of data loader workers |

### 8.4 Training Outputs

After training, weights are saved at:

```text
<project>/<name>/weights/
├── best.pt    ← best validation checkpoint — use this for inference
└── last.pt    ← final epoch checkpoint
```

Training logs, curves, and metrics are also saved in `<project>/<name>/`.

---

## Step 9 — Inference (detect.py)

Once training is complete, use `detect.py` to run the trained model on the test images. This produces per-event prediction `.txt` files in YOLO format.

### 9.1 Environment Setup

```bash
cd /persistent/data1/ms21080/Regression/Data/Model_Nominal
conda activate yolov5
unset LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.6/targets/x86_64-linux/lib:/usr/local/cuda-11.6/lib64
```

### 9.2 Run Detection

```bash
python detect.py \
    --weights '/persistent/data1/ms21080/Regression/Data/Model/runs/train/v1/weights/best.pt' \
    --source '/persistent/data1/ms21080/Regression/Data/Electron/Testing/nPU150/PT200/images/train' \
    --data data/muon47m.yaml \
    --img 736 \
    --conf-thres 0.4 \
    --iou-thres 0.25 \
    --device 0 \
    --save-txt \
    --save-conf \
    --project /persistent/data1/ms21080/Regression/Data/Electron/Testing/nPU150/PT200/labels \
    --name v1
```

**Inference argument reference:**

| Argument | Description |
|---|---|
| `--weights` | Path to `best.pt` from the training run |
| `--source` | Directory of `.npy` test images — use the `images/train/` folder from preprocessing |
| `--data` | Dataset YAML (same one used during training) |
| `--img 736` | Input image size |
| `--conf-thres` | Confidence threshold; predictions below this are discarded |
| `--iou-thres` | IoU threshold for non-maximum suppression |
| `--device` | GPU index for inference |
| `--save-txt` | Save predictions as YOLO-format `.txt` files |
| `--save-conf` | Include confidence score in the saved prediction files |
| `--project` | Root output directory for detection results |
| `--name v1` | Run name; predictions saved to `<project>/v1/labels/` |

### 9.3 Detection Outputs

```text
<project>/v1/labels/
└── <image_name>.txt    ← per-event: class  cx  cy  width  height  confidence
```

These prediction files, combined with the ground-truth `energy/train/` files from preprocessing, are used for the downstream energy regression analysis.

---

## Code Reference

| Script / Repository | Purpose | Link |
|---|---|---|
| `process_root.py` | Convert Step-2 ROOT files to YOLO images, labels, and energy files | [Link](https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/process_root.py) |
| `submit_training.sh` | Batch PBS submission for training data generation | [Link](https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/submit_training.sh) |
| `submit_testing.sh` | Batch PBS submission for testing data generation | [Link](https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/YOLO/submit_testing.sh) |
| `cellwise_segmentation.C` | Segment SimHits into DigiHits (Step-1 → Step-2) | [Link](https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Codes/Segmentation) |
| `01_rootToText.C` | Convert Pythia ROOT file to Geant4-readable text | [Link](https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/PileupUtils/01_rootToText.C) |
| `addPileupToSignal_03.C` | Merge signal and pileup Step-2 files | [Link](https://github.com/sidhu2690/GEANT4/blob/main/HGCAL/Codes/SignalToPileupAdittion/addPileupToSignal_03.C) |
| `HGCAL_YOLOV5_Linear` | YOLOv5 model — 16 input channels | [Link](https://github.com/Sid2690/HGCAL_YOLOV5_Linear) |
| `HGCAL_YOLOV5_Custom` | YOLOv5 model — configurable input channels | [Link](https://github.com/Sid2690/HGCAL_YOLOV5_Custom) |
| `Single_Particle_Simulation` | Geant4 signal event simulation | [Link](https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Single_Particle_Simulation) |
| `Pileup_Simulation` | Geant4 pileup event simulation | [Link](https://github.com/sidhu2690/GEANT4/tree/main/HGCAL/Pileup_Simulation) |

---

## Requirements

All Python dependencies are listed in:

https://github.com/Sid2690/HGCAL_YOLOV5_Custom/blob/main/requirements.txt

Install with:

```bash
pip install -r requirements.txt
```
