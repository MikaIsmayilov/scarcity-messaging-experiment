# BA830 – Scarcity Messaging & Consumer Behavior
### Team 9 | Boston University | Spring 2026

## Overview

This repository contains the full analysis pipeline for a randomized survey experiment examining how **scarcity messaging** (e.g., *"Only 3 left in stock"*) affects consumer purchase behavior. The study was conducted as part of **BA830: Business Experimentation** at Boston University's Questrom School of Business.

**Research Question:** Does displaying low-stock availability messages increase consumers' purchase likelihood, perceived urgency, and anticipated purchase regret?

---

## Experiment Design

| Detail | Description |
|---|---|
| **Platform** | Qualtrics (randomized survey experiment) |
| **Population** | BU undergraduate and graduate students aged 18–25 |
| **Sample Size** | 99 raw responses → **60 usable** (completed, target age range) |
| **Treatment** | Product shown with "Only 3 left in stock" scarcity message |
| **Control** | Same product shown with no availability message |
| **Assignment** | Qualtrics randomized respondents into one of two branches at the survey level |
| **Products** | 3 products evaluated per respondent (within-respondent, cross-product) |

### Outcome Variables (1–5 Likert scale)

| Variable | Question |
|---|---|
| **Purchase Likelihood** | "How likely are you to purchase this item?" |
| **Urgency** | "How urgent does this purchase feel to you?" |
| **Anticipated Regret** | "If you did not purchase this item now, how much regret would you feel?" |

### Covariates Collected

- Age range, gender, student status (undergrad/grad)
- Price sensitivity (1–4 scale)
- Online purchase frequency (1–4 scale)
- Self-reported impulsiveness (1–5 scale)
- Impulse purchase agreement (Likert)
- General scarcity messaging agreement (Likert)

---

## Repository Structure

```
BA830_Team9/
├── BA830_Survey.csv                  # Raw Qualtrics export (99 responses)
├── BA830_Survey_clean_wide.csv       # Cleaned respondent-level dataset (60 × 47)
├── BA830_Survey_clean_long.csv       # Long-format panel dataset (180 × 19)
├── BA830_Survey_codebook.csv         # Variable name → question text mapping
├── Survey_Regression.ipynb           # Full analysis notebook
├── A1_BA830_Team09_Slides.pptx       # Presentation slides
└── BA830 Team9_proposal.docx         # Project proposal
```

---

## Analysis Notebook (`Survey_Regression.ipynb`)

The notebook is organized into two major sections:

### Section 1: Data Cleaning & Preparation

1. **Load raw Qualtrics export** — drops the question-text header row, retains response data
2. **Filter to usable responses** — completed surveys, ages 18–25
3. **Rename & encode demographics** — maps all Likert-scale covariates to numeric values
4. **Assign treatment conditions** — coalesces Qualtrics branch columns (QID45/QID70, etc.) into unified product-level columns; assigns `treat = 1` for scarcity branch
5. **Encode outcome variables** — converts Likert text responses to numeric (1–5)
6. **Build respondent × product panel** — stacks 3 product rows per respondent (180 rows) to enable product fixed-effects models
7. **Covariate balance check** — verifies randomization produced comparable groups
8. **Save cleaned datasets** — exports `_wide`, `_long`, and codebook CSVs

### Section 2: Statistical Analysis

| Model | Method | Purpose |
|---|---|---|
| **2a. T-Test** | Welch's two-sample t-test | Difference-in-means across all three outcomes |
| **2b. OLS (bivariate)** | `outcome ~ treat` with HC robust SEs | Average treatment effect, no controls |
| **2c. OLS with covariates** | `outcome ~ treat + X` | Precision-boosted ATE; controls absorb residual variance |
| **2d. HTE (wide)** | `outcome ~ treat * moderator` | Heterogeneous effects by impulsiveness & price sensitivity |
| **2e. Product FE panel** | `outcome ~ treat \| product`, clustered SEs | Product fixed effects + SEs clustered at respondent level |
| **2f. HTE (long panel)** | `outcome ~ treat * moderator \| product` | HTE within the long panel with product FE |

### Key Results

| Outcome | Control Mean | Treatment Mean | Difference | p-value |
|---|---|---|---|---|
| Purchase Likelihood | 2.531 | 2.606 | +0.075 | 0.715 |
| Urgency | 1.889 | 2.141 | +0.253 | 0.202 |
| Anticipated Regret | 1.642 | 1.859 | +0.217 | 0.342 |

> Scarcity messaging produced **positive directional effects** across all three outcomes — particularly urgency (+0.25) and regret (+0.22) — but results did not reach conventional statistical significance, likely due to sample size limitations.

---

## Setup & Reproduction

### Requirements

```bash
pip install pandas numpy scipy statsmodels pyfixest
```

### Run the Notebook

```bash
jupyter notebook Survey_Regression.ipynb
```

All data files are expected in the same directory as the notebook. The notebook reads `BA830_Survey.csv` and writes the cleaned outputs.

---

## Dependencies

| Library | Purpose |
|---|---|
| `pandas` | Data loading, reshaping, encoding |
| `numpy` | Numeric operations, condition assignment |
| `scipy.stats` | Welch's t-tests |
| `statsmodels` | OLS regression (formula API) |
| `pyfixest` | Fixed-effects OLS, clustered SEs, regression tables |

---

## Team

| Name | |
|---|---|
| Anna Wei | Boston University |
| Erica (Kyuwon) Kim | Boston University |
| Mika Ismayilli | Boston University |
| Pin-Chen Kuo | Boston University |
| Rita Feng | Boston University |

---

## Course

**BA830: Business Experimentation**
Questrom School of Business, Boston University
