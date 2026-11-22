# PCP-LOD-chemical-exposure-analysis
R implementation of Low-Rank and Sparse Principal Component Pursuit (PCP) with Limit of Detection (LOD) awareness for chemical exposure data analysis. Includes chemical data preprocessing, LOD-based filtering, parameter optimization, low-rank and sparse decomposition, and downstream statistical analyses.


PCP-LOD Chemical Exposure Analysis

Overview

This repository contains R scripts for applying Limit-of-Detection-aware Principal Component Pursuit (PCP-LOD) to chemical exposure data.

The workflow includes chemical data preparation, detection-limit classification, detection-frequency filtering, data scaling, hyperparameter optimization, and decomposition of the chemical exposure matrix into low-rank and sparse components.

The objective is to identify underlying exposure patterns while accounting for measurements below the analytical Limit of Detection (LOD).

Methodology

The analysis follows three main stages:

1. Chemical Data Preparation: Select chemical exposure variables, define LOD thresholds, and generate binary indicators identifying measurements below or above the detection limit.
2. Data Cleaning and Preprocessing: Calculate chemical detection frequencies, filter chemicals based on detection frequency, retain eligible participants, and prepare scaled exposure and LOD-related matrices.
3. PCP-LOD Modeling and Analysis: Optimize model hyperparameters, perform low-rank and sparse decomposition, and conduct downstream analyses to characterize exposure patterns.

The decomposition is represented as:

[
X = L + S
]

where:

* (X) represents the processed chemical exposure matrix.
* (L) represents the low-rank component capturing underlying exposure patterns.
* (S) represents the sparse component capturing sparse deviations from the low-rank structure.

Repository Structure

pcp-lod-chemical-exposure-analysis/
│
├── README.md
│
├── R/
│   ├── 01_prepare_chemical_data.R
│   ├── 02_clean_and_prepare_data.R
│   ├── 03_run_pcp_lod_analysis.R
│   ├── functions.R
│   ├── grid_search_cv.R
│   └── pcpr.R
│
├── data/
│   └── README.md
│
├── results/
│   └── README.md
│
└── figures/
    └── README.md

Note: The three main analysis scripts source functions.R, grid_search_cv.R, and pcpr.R. These supporting scripts must also be included in the repository for the workflow to run. The input dataset is not included.

Analysis Workflow

Step 1: Chemical Data Preparation

Script: 01_prepare_chemical_data.R

This script:

* Loads the imputed chemical exposure dataset.
* Selects the chemical biomarkers included in the analysis.
* Defines analyte-specific LOD thresholds.
* Generates binary LOD indicators:
    * 1: Measurement at or below the LOD.
    * 0: Measurement above the LOD.
* Creates a row-based identifier (seqn).
* Exports the resulting dataset as Lod_chemicals.csv.

Step 2: Data Cleaning and Preprocessing

Script: 02_clean_and_prepare_data.R

This script:

* Loads the LOD-classified chemical dataset.
* Standardizes variable names.
* Calculates detection frequencies for each chemical.
* Retains chemicals with detection frequencies greater than the specified threshold (default: 50%).
* Filters participants based on the proportion of available chemical measurements (default: at least 50%).
* Constructs processed exposure and LOD-related matrices.
* Scales chemical exposure measurements using their standard deviations.

The preprocessing function process_pops1() returns:

* scaled_data: Processed and scaled chemical exposure matrix.
* lods: LOD-related matrix used in PCP-LOD modeling.
* sqrt2_data: Scaled imputed exposure matrix.
* seqn_ids: Retained participant row identifiers.
* mask: Binary LOD indicator matrix.

Step 3: PCP-LOD Modeling and Downstream Analysis

Script: 03_run_pcp_lod_analysis.R

This script performs the PCP-LOD analysis.

Hyperparameter optimization

A grid search is conducted over:

* Lambda: 0.5, 1, and 2.
* Mu: 0.5, 1, and 2.
* Rank: 2, 3, 4, and 5.

The grid search uses 10 runs, masks 10% of matrix entries, and evaluates candidate parameter combinations using the specified reconstruction-error procedure.

Low-rank and sparse decomposition

The optimal parameters are used to fit the PCP-LOD model:

[
X = L + S
]

The resulting components are used for downstream analyses.

Downstream analyses

The script includes:

* Singular Value Decomposition (SVD) of the low-rank component.
* Chemical loading visualization.
* Hierarchical clustering of participant-level low-rank patterns.
* Spearman correlation analysis of raw, low-rank, and sparse components.
* Sparse component analysis and classification of high- and low-intensity deviations.

Input Data

The workflow expects an imputed chemical exposure dataset:

ImputedData_NAMED_FLI.csv

The dataset should contain the chemical biomarker variables specified in the data preparation script.

The scripts currently use local file paths. These paths should be updated to match the directory structure of your environment.

The participant-level input dataset is not included in this repository.

Output Files

The analysis generates several intermediate and final outputs, including:

Output	Description
Lod_chemicals.csv	Chemical exposure data with LOD indicators
LowRank_new.csv	Estimated low-rank component
SparseMatrix_new.csv	Estimated sparse component
pops_scaled_new.csv	Processed and scaled chemical exposure matrix
PCPcorrelation_raw_new.csv	Spearman correlation matrix of processed exposure data
PCPcorrelation_lowrank_new.csv	Spearman correlation matrix of the low-rank component
PCPcorrelation_sparse_new.csv	Spearman correlation matrix of the sparse component
svd_loadings.csv	Chemical loadings from SVD

Software and Dependencies

The analysis is implemented in R.

The scripts use several R packages, including:

* dplyr
* readr
* janitor
* ggplot2
* tidyr
* forcats
* Matrix
* softImpute
* foreach
* doParallel
* parallel
* data.table
* plyr
* haven
* corrplot
* pheatmap
* car

Additional custom R scripts (functions.R, grid_search_cv.R, and pcpr.R) are required for the PCP-LOD implementation.

Running the Analysis

Run the scripts in the following order:

# Step 1: Prepare chemical exposure data
source("R/01_prepare_chemical_data.R")
# Step 2: Clean and preprocess chemical exposure data
source("R/02_clean_and_prepare_data.R")
# Step 3: Run PCP-LOD analysis
source("R/03_run_pcp_lod_analysis.R")

Before running the scripts:

1. Place the required input dataset in the appropriate data directory.
2. Include all supporting R scripts.
3. Update file paths to match your local environment.
4. Install the required R packages.

Reproducibility

The repository is intended to document the data preparation, preprocessing, and PCP-LOD modeling workflow.

Reproducibility requires access to the input dataset, supporting R functions, and the appropriate chemical-specific LOD thresholds.

Citation

If you use this code in your research, please cite the associated publication:

Jehu-Appiah, D., & Obeng-Gyasi, E. (2025). Uncovering Exposure Patterns of Metals, PFAS, Phthalates, and PAHs and Their Combined Effect on Liver Injury Markers. Journal of Xenobiotics, 15(6), 178. https://doi.org/10.3390/jox15060178
