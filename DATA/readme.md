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

## 1. Fixed_pT_Step1_Data

**Path:** `/persistent/data1/ms21080/Data/Fixed_pT_Step1_Data`

### Purpose

Contains Geant4 Step-1 simulation output ROOT files for the fixed pT–η setup. These files contain the SimHit information directly produced by Geant4 before digitisation.

### Contents

| Directory | Size |
|------------|------|
| Electron_Step1 | 907 G |
| MuonM_Step1 | 3.8 G |
| Photon_Step1 | 888 G |
| Positron_Step1 | 907 G |

### Example Files

```text
Electron_nPU_000_Pt_025_Eta_170_Events_2K_Set01_Step1.root
Electron_nPU_000_Pt_100_Eta_260_Events_2K_Set01_Step1.root
```

...
