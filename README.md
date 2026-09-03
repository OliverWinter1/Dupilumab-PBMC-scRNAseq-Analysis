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
Leiden clustering produced 13 distinct clusters based on the nearest-neighbour graph constructed using the principle components from the cells. Clusters 1,7 and 8 were the largest clusters and 6, 10 and 12 were the smallest.

Cluster 12 was first considered to be an error cluster especially after it was shown to have very high total counts and was dominanantly present in the AD035_pre. This was shown to be a non-error cluster as marker gene annotation suggested this was a plasma cell cluster. Plasma cells are very transcriptionally active providing a biological reason for why the n_counts was higher than the other clusters.

A marker gene dictionary (see bottom of README) was then used to create a dotplot (Figure 1) to identify the key marker genes that were present in each cluster. The dotplot then enabled each cluster to be labelled with a cell type. The UMAP clustering plot was then labelled with the cell type labels (Figure 2)

### Treatment Comparison 
Once clusters had been labelled by cell-type, the next step was to compare the proportions of each cluster that were pre and post treatment (Table 1). Naive T cells and Plasma cells shows the biggest proportional decrease from pre to post treatment, whereas CD4 T cells showed an increse. However, as only 3 paired patients were available the trends are exploratory and were checked for consistency across the patients. The next stage was to see whether all 3 patients showed the decrease/increase or whether one error sample was carrying the statistic.

Naive T cells showed consistent decrease across all 3 patients. The plasma-cell decrease was not consistent across patients and appeared to be driven mainly by patient AD035. CD4 T cells were also consistent in the increase, meaning we had 2 key cell types to look at. However, as dupilumab's function is to reduce inflammation, it was seen as priority to explore the Naive T cell cluster first as it showed decrease from pre to post treatment.

### Naive T cell exploratory analysis
Each sample contained enough cells in the Naive T cell cluster to allow exploration of the data.
Dupilumab binds to IL4 receptors so the exploratory analysis first targetted genes that were associated with downstream signalling involving the IL4 receptor complex, as dupilumab could inhibit this cascade.
These were: IL4R, IL13RA1, JAK1, JAK2, JAK3, TYK2, STAT6, STAT3, IRS2, GATA3
All these genes were present in the data
A heatmap (Figure 3) was plotted to easily show the gene expression of each gene and each sample. The core IL-4/IL-13 pathway genes showed relatively stable expression between pre- and post-treatment samples. This may be because of subtle transcriptional changes that don't appear in only 3 patients. However, it is also important to note that dupilumab blocks IL4R signalling at the protein level and does not necessarily alter expression of the receptor or core signalling genes themselves.

To explore transcriptional changes beyond the key IL4 associated genes, genes were ranked according to their pre to post treatment expression change in Naive T cells (Table 2). Many of the genes showing the largest decreases were mitochondrial or ribosomal genes, making their biological interpretation more difficult to understand.
However, several of the most consistently increased genes were members of the HLA family, including HLA-A, HLA-B, HLA-C (HLA-C wasn't as consistent as the others however still showed a overall increase) and HLA-E (Table 3). HLA genes are antigen genes and the exploratory analysis could suggest dupilumab causes an increase in antigen presentation of Naive T cells although this is not a statistically established effect due to the small sample size.

## Key Exploratory Findings
- Leiden clustering produced 13 clusters
- Naive T cells showed the most consistent cell decrease between pre and post treatments
- CD4 T cells showed the most consistent cell increase between pre and post treatments
- Core IL4 signalling cascade genes showed stable expression between pre and post treatments in Naive T cells
- Several HLA genes showed consistent post treatment increases in Naive T cells 

## Limitations
- Only 3 paired samples
- 1 pair had to be excluded due to poor data
- No statistical testing due to small sample size 
- All conclusions are exploratory 

## Marker Gene Dictionary
T cells: CD3D, CD3E, TRAC 
CD4 T cells: IL7R, LTB, MAL, CCR7 
Naive T cells: CCR7, SELL, TCF7, LEF1, MAL 
CD8 T cells: CD8A, CD8B, CCL5, NKG7, GZMK 
Regulatory T cells: FOXP3, IL2RA, CTLA4, TIGIT 
NK cells: NKG7, GNLY, KLRD1, PRF1, GZMB 
B cells: MS4A1, CD79A, CD79B, CD74, CD37 
Naive B cells: IGHD, IGHM, TCL1A, FCER2, IL4R 
Memory B cells: CD27, CD37, CD79A, CD82, TNFRSF13B
Plasma cells: MZB1, JCHAIN, XBP1, SDC1, DERL3 
Classical monocytes: LYZ, S100A8, S100A9, CTSD, FCN1, CD14 
Non-classical monocytes: FCGR3A, MS4A7, LST1, IFITM3, LILRB1 
cDC2: CD1C, FCER1A, CLEC10A, CST3, HLA-DRA 
cDC1: CLEC9A, BATF3, CADM1, XCR1 
pDC: LILRA4, GZMB, PLD4, IL3RA, TCF4 
Platelets: PPBP, PF4, NRGN, GNG11 
