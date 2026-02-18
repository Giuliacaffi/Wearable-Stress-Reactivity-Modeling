# Capturing the Stress Jump  
## Predicting Physiological Reactivity from Empatica E4 Signals

Wearable Devices Course Project  
MSc in Health Informatics – A.Y. 2025–26  

Authors:  
Giulia Caffi (MSc Medicine and Surgery)  
Rebecca Metallo (BSc Mathematical and Computing Sciences for AI)  
Anna Uberti (BSc Medical Radiology Techniques)

---

## Project Overview

Psychological stress induces measurable autonomic responses, including:

- Increased heart rate
- Reduced heart rate variability
- Increased electrodermal activity
- Peripheral vasoconstriction (reduced skin temperature)

Recent wearable technologies enable continuous monitoring of these physiological signals.  

This project investigates whether wrist-based physiological signals recorded by the **Empatica E4** can predict *within-subject stress reactivity* during a controlled Stress Induction Protocol.

The central research question is:

> Can we predict how much an individual becomes stressed relative to their own baseline level using short physiological recordings?

The study is based on the public dataset available on PhysioNet:  
https://physionet.org/content/wearable-device-dataset/1.0.1/

---

## Dataset

We used only the **Stress Protocol** session.

Additional metadata files included:

- `stress_level_v1.csv`
- `stress_level_v2.csv`
- `subject_info.csv`

### Physiological Signals (Empatica E4)

| Signal | Acronym | Description |
|--------|---------|------------|
| Electrodermal Activity | EDA | Sympathetic nervous system activity |
| Blood Volume Pulse | BVP | Optical signal related to cardiac cycles |
| Accelerometer | ACC | Triaxial movement |
| Skin Temperature | TEMP | Peripheral temperature |
| Heart Rate | HR | Derived from BVP |
| Inter-Beat Interval | IBI | Time between heartbeats |

(See Table 1 in the project report  [oai_citation:1‡WD_ProjectReport.pdf](sediment://file_0000000040e871f8a028bc9564f8a025))

---

## Preprocessing Pipeline

### 1. Signal Segmentation

- Signals aligned to protocol phases
- Windows extracted for:
  - Baseline
  - Stroop / TMCT
  - Rest
  - Opinion tasks
  - Subtraction

Non-relevant portions removed.

Participants with >15% missing data were excluded.

---

### 2. Feature Extraction

For each signal and each phase:

- Mean
- Standard deviation

Extended dataset included HRV features derived from IBI:

- RMSSD
- pNN50
- CVRR
- CVSD

Feature selection performed using:

- Minimum Redundancy Maximum Relevance (mRMR)

---

### 3. Target Construction

We defined stress reactivity as:

Δ-stress = stress_level − baseline_stress

Two modeling strategies were explored:

1. Regression (continuous Δ-stress)
2. Classification (Δ-stress > 0 vs ≤ 0)

---

## Modeling Approaches

### Model 1 — Random Forest Regressor (Summary Statistics)

Validation: Hold-out  
R² = 0.176  
RMSE = 1.637  

Anthropometric variables (height, weight) dominated feature importance.

---

### Model 2 — Random Forest + HRV Features

Validation: Hold-out  
R² = 0.191  
RMSE = 1.623  

IBI-derived metrics provided marginal improvement.

---

### Model 3 — Gradient Boosting Regressor

Validation: Hold-out  
R² = 0.121  
RMSE = 1.690  

Performance decreased compared to Random Forest.

---

### Model 4 — Gradient Boosting with Baseline Normalization

Features transformed into:

Δ-feature = phase_value − baseline_value

Validation: Leave-One-Subject-Out (LOSO)  

Global R² = −0.120  

Generalization across subjects failed.

---

### Model 5 — XGBoost Classification

Task reformulated as:

Binary target = 1 if Δ-stress > 0 else 0

Validation: LOSO  

Accuracy = 0.514  
Balanced Accuracy = 0.524  
AUC = 0.593  

Classification was more stable than regression but remained close to chance.

(See performance summary Table 3  [oai_citation:2‡WD_ProjectReport.pdf](sediment://file_0000000040e871f8a028bc9564f8a025))

---

## Key Findings

- Physiological stress effects are subtle and noisy in wrist-worn devices.
- Short task windows reduce HRV reliability.
- Anthropometric variables often dominate tree-based models.
- Baseline normalization alone does not guarantee generalization.
- Subjective self-reported stress is a noisy target variable.
- Wearable-derived signals alone are insufficient to reliably model fine-grained stress reactivity.

---

## Scientific Contribution

This project highlights:

- The limitations of short wearable recordings for stress modeling.
- The importance of validation strategies such as LOSO.
- The risk of anthropometric bias in small physiological datasets.
- The difficulty of modeling subjective stress from peripheral physiology.

Rather than demonstrating strong predictive performance, this work provides a critical evaluation of wearable-based stress modeling.
