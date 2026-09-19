# BCRS-28: Breast Cancer Risk Signature-28

> **A Single-Cell-Derived Transcriptomic Classifier for Early Breast Cancer Detection in BRCA1-Mutant Normal Mammary Tissue**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![R version](https://img.shields.io/badge/R-%3E%3D4.2-blue.svg)](https://www.r-project.org/)
[![Manuscript](https://img.shields.io/badge/Journal-IJMS-green.svg)](https://www.mdpi.com/journal/ijms)

---

## Overview

This repository contains the complete analysis code for the manuscript:

> **"A Pre-Malignant Transcriptomic Signature in BRCA1-Loss Breast Epithelium Enables Machine Learning-Based Early Cancer Detection"**  
> Zinab O. Doha  
> *International Journal of Molecular Sciences*, 2026  
> Manuscript ID: ijms-4533345

BCRS-28 is a 28-gene transcriptomic signature derived from single-cell RNA sequencing of histologically **normal** BRCA1-mutant breast epithelium. It captures pre-malignant molecular reprogramming and, when applied to multi-cohort bulk RNA-seq data with a Support Vector Machine (SVM) classifier, achieves AUC = 0.992 in training, 0.978 in internal testing, and **0.964 in fully independent external validation** (GSE58135).

---

## Repository Structure

```
BCRS28-analysis/
├── BCRS28_1_scRNAseq.Rmd        # Script 1: scRNA-seq analysis
├── BCRS28_2_ML_model.Rmd        # Script 2: Bulk RNA-seq + ML model
├── BCRS28_3_Spatial.Rmd         # Script 3: Spatial transcriptomics
└── README.md                    # This file
```

---

## Analysis Scripts

### Script 1 — scRNA-seq Analysis (`BCRS28_1_scRNAseq.Rmd`)

Single-cell RNA sequencing analysis of BRCA1-mutant vs wild-type normal breast epithelium.

| Section | Description |
|---|---|
| Step 1–2 | Library loading, object import |
| Step 3 | Normalization, PCA, clustering, UMAP |
| Step 4 | Cell type annotation (canonical markers) |
| Step 5 | Figure 1 — single-cell atlas |
| Step 6 | Epithelial cell subset |
| Step 7 | Differential expression (Wilcoxon + DESeq2 pseudobulk) |
| Step 8 | Figure 2 — volcano plot, heatmap |
| Step 9 | Pathway enrichment (GO, KEGG, GSEA Hallmark) |
| Step 10 | Supplementary Tables S1–S8 |

**Output:** Figures 1–2, Supplementary Tables S1–S4, S7–S8

---

### Script 2 — Bulk RNA-seq & ML Model (`BCRS28_2_ML_model.Rmd`)

Multi-cohort bulk RNA-seq integration and BCRS-28 SVM classifier development.

| Section | Description |
|---|---|
| Step 2 | Dataset download and processing (TCGA, GSE183947, GSE58135) |
| Step 3 | **Two-stage ComBat batch correction** (training only → fixed transform to validation) |
| Step 4 | PCA quality control (Figure 2B–C) |
| Step 5–6 | ML feature matrix, 80/20 stratified split |
| Step 7 | 5-fold cross-validation functions |
| Step 8 | Stepwise model comparison — 7 classifiers (Figure 3A–C) |
| Step 9 | Fine-grained SVM optimization → 28-gene optimum (Figure 3D–F) |
| Step 10 | Final BCRS-28 SVM training + comprehensive metrics |
| Step 11 | Figure 4 — ROC, PR, calibration, score distribution |
| Step 12 | Figure 5 — performance heatmap + decision curve analysis |
| Step 13 | Supplementary Table S9 |

**Output:** Figures 3–5, Supplementary Tables S5, S9

---

### Script 3 — Spatial Transcriptomics (`BCRS28_3_Spatial.Rmd`)

Spatial mapping of BCRS-28 signature onto 10x Visium breast carcinoma data.

| Section | Description |
|---|---|
| Step 2 | GEO download (GSE203612, NYU_BRCA0 Visium) |
| Step 3 | Load and inspect Visium object |
| Step 4 | Normalize, check gene availability |
| Step 5 | Directional module score (up − down) |
| Step 6 | Figure 6 — spatial score map, High/Low classification, distribution |
| Step 7 | Individual gene spatial maps |
| Step 8 | Spot-level statistics summary |

**Output:** Figure 6, Supplementary spatial summary table

---

## Data Sources

| Dataset | Accession | N samples | Source | Role |
|---|---|---|---|---|
| BRCA1 vs WT scRNA-seq | GSE161529 | 47,066 cells | GEO | scRNA-seq discovery |
| TCGA-BRCA | — | 1,211 | UCSC Xena | Training |
| GSE183947 | GSE183947 | 60 | GEO | Training |
| GSE58135 | GSE58135 | 135 | GEO | **Independent validation** |
| NYU Visium | GSE203612 | 2,384 spots | GEO | Spatial support |

> **TCGA download:** https://xenabrowser.net/datapages/?cohort=TCGA%20Breast%20Cancer%20(BRCA)

---

## BCRS-28 Gene Signature

The 28-gene BCRS-28 panel, grouped by functional category:

| Group | Direction | Genes |
|---|---|---|
| DNA Damage Response | ↑ UP | GADD45G |
| Established BC Biomarkers | ↑ UP | CRIP1, SNCG, PIP |
| Epithelial Identity & Keratins | ↑ UP | KRT14, KRT15, MUCL1, SERHL2 |
| Oncogenic Signalling | ↑ UP | ANGPTL4, BAMBI, CD14, CXCL14 |
| Proteases & Metabolism | ↑ UP | KLK7, KLK5, RARRES1, SLC40A1, FABP3 |
| Apoptosis & Tumour Suppression | ↓ DOWN | TGM2, HMOX1, SCGB3A1, SERPINA5, FASN |
| Cell Signalling & Structural | ↓ DOWN | WNT4, LMO7, CCL20, EEF1A2, CCDC74A, GBP1 |

---

## Key Results

| Cohort | AUC | Sensitivity | Specificity | AUPRC | MCC |
|---|---|---|---|---|---|
| Training (TCGA+GSE183947) | 0.992 | 0.978 | 0.991 | 0.998 | 0.915 |
| Test (20% holdout) | 0.978 | 0.844 | 1.000 | 0.993 | 0.791 |
| **Independent Validation (GSE58135)** | **0.964** | **0.976** | **0.863** | **0.961** | **0.551** |

> GSE58135 was batch-corrected **independently** — ComBat parameters estimated from training cohorts only and applied as a fixed transformation to GSE58135, preserving true external validation independence.

---

## Requirements

### R Version
R ≥ 4.2.0

### Required Packages

**Script 1 (scRNA-seq):**
```r
install.packages(c("tidyverse","patchwork","pheatmap","ggrepel","openxlsx"))
BiocManager::install(c("Seurat","DESeq2","clusterProfiler",
                        "org.Hs.eg.db","enrichplot","msigdbr"))
```

**Script 2 (ML model):**
```r
install.packages(c("tidyverse","patchwork","e1071","randomForest",
                   "gbm","nnet","rpart","class","PRROC","dcurves","openxlsx"))
BiocManager::install(c("pROC","sva"))
```

**Script 3 (Spatial):**
```r
install.packages(c("tidyverse","patchwork","ggplot2","scales"))
BiocManager::install("Seurat")
```

---

## How to Run

### 1. Clone the repository
```bash
git clone https://github.com/[username]/BCRS28-analysis.git
cd BCRS28-analysis
```

### 2. Set up directory structure
```
BCRS28-analysis/
├── data/
│   ├── raw/
│   │   └── spatial/GSE203612/
│   ├── processed/          ← place processed RDS files here
│   └── integrated/         ← created automatically
├── figures/
│   ├── ML/
│   └── spatial/
├── supplementary/
└── signature_genes.xlsx    ← BCRS-28 gene list with log2FC
```

### 3. Run scripts in order
```r
# In RStudio: Knit each script in order
# Script 1 → Script 2 → Script 3
rmarkdown::render("BCRS28_1_scRNAseq.Rmd")
rmarkdown::render("BCRS28_2_ML_model.Rmd")
rmarkdown::render("BCRS28_3_Spatial.Rmd")
```

---

## Input Files Required

| File | Description | Used in |
|---|---|---|
| `seu_wt_brca1_with_metadata.rds` | Processed scRNA-seq Seurat object | Script 1 |
| `signature_genes.xlsx` | BCRS-28 genes with log2FC and direction | Script 2 |
| `data/processed/TCGA_BRCA_processed.rds` | Processed TCGA expression + metadata | Script 2 |
| `data/processed/GSE183947_processed.rds` | Processed GSE183947 expression + metadata | Script 2 |
| `data/processed/GSE58135_processed.rds` | Processed GSE58135 expression + metadata | Script 2 |
| GSE203612 Visium files | Downloaded automatically in Script 3 | Script 3 |

---

## Output Files

| File | Description |
|---|---|
| `figures/Figure1_atlas.png` | scRNA-seq cell type atlas |
| `figures/Figure2B_volcano.png` | Differential expression volcano plot |
| `figures/Figure2C_heatmap.png` | Top 25 up/down gene heatmap |
| `figures/ML/Figure3ABC_model_comparison.png` | 7-model stepwise comparison |
| `figures/ML/Figure3DEF_SVM_optimal.png` | Fine-grained SVM optimization |
| `figures/ML/Figure4_BCRS28_Performance.png` | Full performance panel |
| `figures/ML/Figure5_Summary_DCA.png` | Performance heatmap + DCA |
| `figures/spatial/Figure6_BCRS28_Spatial.png` | Spatial signature distribution |
| `figures/FigS1D_BCRS28_Donor_Heatmap.png` | Donor-level consistency heatmap |
| `supplementary/Tables_S1_S4_DE_Results.xlsx` | Full DE results |
| `supplementary/TableS7_BCRS28_Pseudobulk.csv` | Pseudobulk DESeq2 results |
| `supplementary/TableS8_Donor_Consistency.csv` | Donor-level consistency |
| `supplementary/TableS9_BCRS28_Performance.csv` | Full performance metrics |

---

## Citation

If you use this code, please cite:

```bibtex
@article{doha2026bcrs28,
  title   = {A Pre-Malignant Transcriptomic Signature in BRCA1-Loss Breast
             Epithelium Enables Machine Learning-Based Early Cancer Detection},
  author  = {Doha, Zinab O.},
  journal = {International Journal of Molecular Sciences},
  year    = {2026},
  note    = {Manuscript ID: ijms-4533345}
}
```

---

## Spatial Data Reference

Barkley D, Moncada R, Pour M, et al. Cancer cell states recur across tumor
types and form specific interactions with the tumor microenvironment.
*Nat Genet.* 2022;54(8):1192–1201. doi:10.1038/s41588-022-01141-9

---

## License

This project is licensed under the MIT License.

---

## Contact

**Dr. Zinab O. Doha**  
Taibah University  
📧 ztoha@taibahu.edu.sa
