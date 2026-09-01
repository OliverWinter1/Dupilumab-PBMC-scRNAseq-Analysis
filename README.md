# Analysis of scRNAseq data of PMBC cells to anaylyse effects of dupilumab pre and post treatment

## Project Overview
The aim of this project was to analyse the effect of dupilumab on Peripheral Blood Mononuclear Cells (PBMC cells). This was done by comparing the cell proportions and gene expression counts between the pre-treatment samples and the post-treatment samples. 

The project involved a scanpy workflow in order to preproccess and cluster the scRNAseq data which then allowed for exploratory analysis to be conducted.

## Biological Aim
Atopic dermatitis (AD) is an inflammatory skin condition characterized in part by elevations in type 2 immune cytokines such as IL-4 and IL-13. Dupilumab is a monoclonal antibody that targets the shared receptor for these cytokines and has shown remarkable efficacy in reducing inflammation in AD.

The purpose of this study was to anaylse the effects of dupilumab on circulating immune cells 

## Data 
The dataset used in this project is a PBMC single-cell RNA-seq dataset stored in 8 10x Genomics raw_feature_bc_matrix.h5 files that were concatenated after preprocessing.

The study followed a paired design, each post-treatment sample is matched with a pre-treatment sample from the same patient which enables precice comparison later down the workflow

Four paried patients were originally used with 8 datasets however, 1 patient pairing had to be removed due to insufficient cell counts in the post-treatment dataset so this patient was not used. This left 3 patients and 6 paired datasets.

GEO accession: GSE183953
Contributor(s)	Ver Heul A, Mack M, Kim B

## Workflow:
#### -Load the libraries
#### -Load the first file
- Loaded the first samples pre-treatment raw_feature_bc_matrix.h5 file
#### -Inspect the obs (cell metadata)
- The raw cell metadata contained 546771 cell barcodes and 36601 genes
- There is currently no cell metadata so no collumns are shown apart from the cell indexing
- The patient, treatment and sample was then added to the cell metadata
#### -Inspect the var (gene metadata)
- The gene names were made unique
#### -Calulated quality control metrics
- The quality control metrics showed some genes showing 0 total_counts and n_genes_by_counts
- 193241 out of 546771 cell barcodes showed no signal (35%)
- However, cell barcodes can also pick up low signals even if they are blanks so a violin plot was plotted to show the distribution of total_counts and n_genes_by_counts
- It was decided to filter out all cell barcodes with a minimum 250 n_genes_by_counts 
#### -Select HVG (Highly Variables Genes)
#### -Undergo feature selection and PCA
- PCA was used to further reduce dimensionality
- 50 PCA components capture major patterns in variation, the 30 more important were used.
#### -Construct nearest neighbor plots
- Built using the PCA representation of the data
- It connects cells with similar gene expression profiles and maps them close together
#### -Generate UMAP plot
#### -Perform clustering
- We used Leiden clustering to group transcriptionaly similar cells
#### -Reasses quality control and remove error cells