# Data Directory Structure

## Overview

This directory contains all datasets used throughout the HGCal simulation and reconstruction workflow, including:

- Geant4 simulation outputs (Step-1 and Step-2)
- Fixed and variable pT/η signal samples
- Pythia8-generated pileup events
- Geant4 pileup simulations
- Signal + pileup merged datasets
- Production and job scripts

**Base Path:** `/persistent/data1/ms21080/Data`  
**Total Storage:** ~12 TB

---

## Directory Structure

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

---

## 1. Fixed_pT_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Fixed_pT_Step1_Data`

### Purpose

Contains Geant4 Step-1 simulation output ROOT files for the fixed pT–η setup. These files contain the SimHit information directly produced by Geant4 before digitisation.

### Contents

| Directory | Size |
|---|---|
| Electron_Step1 | 907 G |
| MuonM_Step1 | 3.8 G |
| Photon_Step1 | 888 G |
| Positron_Step1 | 907 G |

### Example Files

```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step1.root
Electron_nPU_000_Pt_100_Eta_260_Events_2K_Set01_Step1.root
```

> Separate directories exist for each particle type.

---

## 2. Variable_pT_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Variable_pT_Step1_Data`

### Purpose

Contains Geant4 Step-1 simulation outputs generated using the variable pT–η configuration. Unlike the fixed-pT dataset, these samples cover a continuous range of pT and η values. These files contain the SimHit information directly produced by Geant4 before digitisation.

### Contents

| Directory | Size |
|---|---|
| Electron_Step1 | 171 G |
| MuonM_Step1 | 725 M |
| Photon_Step1 | 164 G |
| Positron_Step1 | 170 G |

### Example Files

```text
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set01_Step1.root
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set02_Step1.root
```

> These files contain Geant4 SimHit information for variable pT and η ranges.

---

## 3. Fixed_pT_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Fixed_pT_Step2_Data`

### Purpose

Contains the digitised version of the Step-1 datasets. The Step-1 ROOT files are processed through the cell-wise segmentation/digitisation code, which converts Geant4 SimHits into detector cell-level information.

### Contents

| Directory | Size |
|---|---|
| Electron_Step2 | 15 G |
| MuonM_Step2 | 1.6 G |
| Photon_Step2 | 13 G |
| Positron_Step2 | 14 G |

### Example Files

```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step2.root
Electron_nPU_000_Pt_025_Eta_185_Events_2K_Set01_Step2.root
```

### Processing Flow

```
Step1 ROOT File
      ↓
Cell-wise Segmentation / Digitisation
      ↓
Step2 ROOT File
```

---

## 4. Variable_pT_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Variable_pT_Step2_Data`

### Purpose

Contains digitised detector outputs corresponding to the variable pT–η Step-1 datasets.

### Contents

| Directory | Size |
|---|---|
| Electron_Var_Step2 | 2.4 G |
| MuonM_Var_Step2 | 64 M |
| Photon_Var_Step2 | 2.1 G |
| Positron_Var_Step2 | 2.4 G |

### Example Files

```text
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set01_Step2.root
Electron_nPU_000_Pt_015_to_250_Eta_16_29_Events_2K_Set02_Step2.root
```

---

## 5. PileUp_Pythia_Data

**Path:** `/persistent/data1/ms21080/Data/PileUp_Pythia_Data`

### Purpose

Contains pileup events generated using Pythia8. These files serve as the starting point for the pileup simulation workflow.

### Contents

| File | Size | Description |
|---|---|---|
| Pythia8_PU_Events_20K_v0.root | 1.8 G | Hard-QCD interaction sample |
| Pythia8_PU_Events_20K_v1.root | 72 M | Soft-collision pileup sample |
| Pythia8_PU_Events_20K_v2.root | 98 M | Updated soft-collision pileup sample (latest) |
| Final_Bharat/ | — | Final pileup files for Geant4 input |

### Final_Bharat Subdirectory

**Path:** `/persistent/data1/ms21080/Data/PileUp_Pythia_Data/Final_Bharat`

| File | Size | Description |
|---|---|---|
| Bharat_final_root.root | 72 M | Final pileup ROOT file used throughout this project |
| rootToText.C | 4.0 K | Converts ROOT file to Geant4-compatible text format |
| generated_data.txt | 11 M | Generated text file used as input for Geant4 pileup simulation |

### Workflow

```
Bharat_final_root.root
        ↓
    rootToText.C
        ↓
generated_data.txt
        ↓
Geant4 Pileup Simulation
```

---

## 6. Pileup_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Pileup_Step1_Data`

### Purpose

Contains the Geant4 Step-1 simulation outputs generated from the Pythia pileup events.

### Contents

| File | Size |
|---|---|
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v0.root | 68 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v1.root | 8.1 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step1_v2.root | 11 G |

> Each version corresponds directly to the matching Pythia pileup sample (v0, v1, v2).

---

## 7. Pileup_Step2_Data

**Path:** `/persistent/data1/ms21080/Data/Pileup_Step2_Data`

### Purpose

Digitised detector-level outputs corresponding to the pileup Step-1 datasets.

### Contents

| File | Size |
|---|---|
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v0.root | 5.7 G |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v1.root | 856 M |
| PileUp_Pt_GT_pt3_Eta_15_31_Events_20K_Step2_v2.root | 1.1 G |

### Workflow

```
Pileup Step1
      ↓
Digitisation
      ↓
Pileup Step2
```

---

## 8. Signal_PileUp_Data

**Path:** `/persistent/data1/ms21080/Data/Signal_PileUp_Data`

### Purpose

Contains the final datasets produced after merging signal events with pileup events.

### Contents

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

### Example Files

```text
Electron_nPU_035_Pt_025_Eta_170_Events_2K_Set01_Step2.root
Electron_nPU_100_Pt_025_Eta_245_Events_2K_Set01_Step2.root
```

> The `nPU` field indicates the number of pileup interactions merged with the signal event.  
> `nPU_035` → 35 pileup interactions | `nPU_100` → 100 pileup interactions

### job Subdirectory

**Path:** `/persistent/data1/ms21080/Data/Signal_PileUp_Data/job`

Contains scripts and batch-job infrastructure used to merge signal and pileup datasets.

```text
job/
├── addPileupToSignal.C
├── job.sh
├── PileUp.txt
├── electron/
├── electron_fix/
├── muonm/
├── muonm_fix/
├── photon/
├── photon_fix/
├── positron/
├── positron_fix/
└── set/
```

> Used for signal–pileup merging production. Contains submission scripts, configuration files, and logs. Primarily serves as an intermediate production directory and is not required for downstream analysis.

---

## Overall Data Processing Pipeline

```
Pythia Pileup Generation
         ↓
 Bharat_final_root.root
         ↓
      rootToText.C
         ↓
  generated_data.txt
         ↓
 Geant4 Simulation
         ↓
    Step1 Data
 (SimHit Information)
         ↓
    Digitisation
         ↓
    Step2 Data
(Cell-level Detector Data)
         ↓
Signal + Pileup Merging
         ↓
Ready for YOLO Preprocessing
```
