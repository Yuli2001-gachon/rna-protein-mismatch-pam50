# RNA–protein mismatch and PAM50 breast-cancer subtypes

Analysis code for

> **Statistical Analysis of RNA-Protein Mismatch and Its Association with Breast Cancer Subtypes**
> Yuli Kim, Hyunsu Kim, Kangjin Kim — Department of Applied Statistics, Gachon University (manuscript in preparation)

The whole analysis is a single R Markdown document, `code/RNA_Protein_Mismatch_analysis.Rmd`,
which regenerates every table and figure of the manuscript and its supplement.

## What it does

1. Loads the CPTAC BRCA 2018 prospective cohort (Krug et al., *Cell* 2020) and builds the analysis set
   (110 patients, 2,970 genes: complete RNA and protein, RNA MAD top 50%).
2. Defines RNA–protein mismatch per gene as the residual of
   `protein ~ RNA + TMT batch + tumour purity + necrosis`, fitted on training folds only.
3. Compares seven random-forest models (RNA, Protein, Mismatch and their combinations) with
   leakage-free nested cross-validation (outer 5-fold, inner 3-fold) and paired stratified bootstrap CIs.
   Primary metric: macro F1; macro balanced accuracy, accuracy and Cohen's kappa are secondary.
4. Repeats the comparison under fixed-budget sparse selection (sPLS-DA / DIABLO, mixOmics).
5. Screens genes whose subtype signal is specific to mismatch (permutation test), identifies stable
   mismatch markers across folds, and characterises the Luminal B signal (variance vs location,
   shift decomposition, Shapley values, incremental value, nested repeated cross-validation).

## Input data (not included)

Download the following files and place them in a folder named `inputdata/` next to `code/`
(or point the environment variable `CPTAC_INPUT_DIR` to their folder):

| file | source |
|---|---|
| `HS_CPTAC_BRCA_2018_CLI.tsi` | [LinkedOmics](https://www.linkedomics.org) — clinical table (PAM50, ESTIMATE purity, necrosis) |
| `HS_CPTAC_BRCA_2018_RNA_GENE.cct` | LinkedOmics — RNA-seq gene expression |
| `HS_CPTAC_BRCA_2018_Proteome_Ratio_Norm_gene_Median.cct` | LinkedOmics — TMT protein abundance |
| `S039_Breast_Cancer_Prospective_Collection_Specimens_r1.xlsx` | [CPTAC Data Portal](https://proteomics.cancer.gov) — specimen table with TMT experiment |

## Run

```r
# from the code/ folder
rmarkdown::render("RNA_Protein_Mismatch_analysis.Rmd", envir = globalenv())
```

The document must be knit in the global environment (some sensitivity sections temporarily
override global settings). Outputs are written to `code/table/` and `code/figure/`.
All random procedures use fixed seeds. Packages and versions used for the manuscript are in
`code/sessionInfo.txt` (R 4.6.1, randomForest 4.7-1.2, mixOmics 6.36.0, tidyverse 2.0.0,
flextable, officer, readxl).

Comments inside the R Markdown are in Korean.
