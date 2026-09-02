# Exploratory analysis of scRNAseq data of PMBC cells to anaylyse effects of dupilumab pre and post treatment

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

- GEO accession: GSE183953
- Contributor(s): Ver Heul A, Mack M, Kim B

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
- QC metrics were then reassessed 
#### -Further cell filtering (Mitochondrial)
- Mitochondrial genes were identified and then a violin plot was created to show the distribution of percentage of genes that are mitochondrial in each cell
- Cells that were over 20% mitochondrial genes were removed leaving 6655 cells
- This is because a cells RNA makeup should be mostly nuclear, cells with high mitochondrial gene expression are often stressed or damaged
#### -Further cell filtering (Doublet)
- The function scrublet was then used to identify predicted doublets
- 223 doublets were identified and removed which left 6432 cells
#### -Save the raw counts
- The raw counts were saved and the dataset was renamed to match the sample
#### -Run the previous preprocessing on the other 7 datasets
- A function was defined to automatically conduct the previous preprocessing steps that had been done on the first sample under the assumption that these 8 datasets were similar
- Patient AD036 had to be removed due to the post-treatment sample only having 446 cell barcodes with >250 genes meaning this sample was too small to be removed
- All other samples ended up with between 4000 and 11,000 usable cells 
-The 6 datsets were then concatenated 
#### -Normalisation
#### -Select HVG (Highly Variables Genes)
- The highly variable gene function was used to select the top 2000 HVGs
- This reduced the dimensionality whilst allowing the most influential genes to be used in clustering
#### -Undergo feature selection and PCA
- PCA was used to further reduce dimensionality
- 50 PCA components capture major patterns in variation, the 40 most important were used
#### -Construct nearest neighbor plots
- Built using the PCA representation of the data
- It connects cells with similar gene expression profiles and maps them close together
#### -Generate UMAP plot
#### -Perform clustering
- We used Leiden clustering to group transcriptionaly similar cells
#### -Inspect clusters
#### -Cluster 12 analysis
- This was due to cluster 12 having abnormally high n_counts
- This was shown to be a non-error cluster as the marker genes suggested this was a plasma cell cluster
- Plasma cells are very transcriptionally active suggesting why the n_counts was higher than all the other clusters
#### -Marker gene dictionary
#### -Marker gene dotplot
#### -Cluster Labelling
#### -Treatment Comparison 
#### -Naive T cell analysis

## Exploratory Analysis
### Cell-type annotation
Leiden clustering produced 13 distinct clusters based on the previous neighboring graph and how similar each cell was to its neighbor. Clusters 1,7 and 8 were the largest clusters and 6, 10 and 12 were the smallest.

Cluster 12 was perculiar and the majority of it's population was from sample AD035_pre. Cluster 12 was first thought to be an error cluster especially after it was shown to have very high total counts. This was shown to be a non-error cluster as the marker genes suggested this was a plasma cell cluster. Plasma cells are very transcriptionally active suggesting why the n_counts was higher than all the other clusters.

A marker gene dictionary was then used to create a dotplot (Figure 1) to identify the key marker genes that were present in each cluster. The dotplot then enabled each cluster to be labelled with a cell type. The UMAP clustering plot was then labelled with the cell type labels (Figure 2)

### Treatment Comparison 
