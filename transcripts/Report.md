# Construction of a Gene Co-expression Network Using RNA-seq Data from Watered(W) Samples #


__Dataset:__ RNA-seq Transcriptomes-Watered(W) Conditions


__Authors:__ DORRA SDIRET & AYCEL LINE DJOUAMA


__Date:__ 25/01/2026

## 1. Overview

The report outlines the construction of gene co-expression network using WGCNA analysis on RNA-seq transcriptomic data obtained from watered (W) samples. This step aims to identify co-expressed gene modules and relate them to phenotypic traits


## 2. Data Pre-processing and Quality Control

The input data TPM_counts_Drought_W_dataset.csv  it's a csv file that contains transcripts-level expression values TPM. 
If we have a look on the data, the first column corresponds to isoform identifiers, while the remaining columns correspond to biological samples. 
After we import the data in RStudio, the expression matrix was transposed so that rows represented samples and columns represented isoforms, as required by WGCNA. 
In this step we use the following command : 

<img width="1014" height="276" alt="image" src="https://github.com/user-attachments/assets/ba9c97d2-315a-4573-a276-862dcaa37158" />

The next step, aims to check the genes and the samples and to remove genes with too many missing samples in W data. 
Durig this step we used the goodSamplesGenes() function, but no genes were removed at this step. The number of isoforms here were 25640. 

<img width="1007" height="124" alt="image" src="https://github.com/user-attachments/assets/367c308d-9dca-4d5a-87b3-f4730399193a" />



=> After filtering the dataset comprised __39 samples__ and __9,940 isoforms__ .

<img width="1017" height="338" alt="image" src="https://github.com/user-attachments/assets/d3e7aca0-374d-4114-bcd3-ffe3964942af" />


## 3. Outlier Detection 

In this step we performed an Hierarchical Clustering of samples using average linkage and Euclidean distance. 
We applied a cut height of 300000 and a minimum cluster size of 20 samples. 

  <img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/823f34cc-d29b-4012-9208-6ceb143189c2" />

All samples were clustered within the main group , and therefore no samples were discarded during the outlier analysis as shown in the clustering plot below. 


## 4. Network construction and module detection


To procede with constructing the co-expression network, we must select a soft-thresholding power using the scale-free topology criterion.
Based on the scale-independence plot, a power value of β = 6 was chosen, as it was the lowest power achieving a scale-free topology fit index (R²) ≥ 0.85.

<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/8b82c151-74bc-449e-89ca-8ea870d2f34f" />



An unsigned adjacency matrix was calculated, followed by computation of the Topological Overlap Matrix (TOM). Genes were hierarchically clustered based on TOM dissimilarity, and modules were detected using dynamic tree cutting with a minimum module size of 30 transcripts.

<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/fe8d7d70-aef7-4846-a47a-262fc1a713ef" />


<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/ecce7446-de05-40a9-ac37-328fec1e409a" />


## 5. Module merging 

This step aims to reduce redundancy among closely related modules by applying module merging. 
For each detected module, a module eigengene (ME) was calculated. The module eigengene represents the first principal component of the expression matrix of all isoforms within a module and can be interpreted as the overall expression profile of that module across samples.

Pairwise Pearson correlations were then computed between all module eigengenes. Modules whose eigengenes exhibited high similarity (i.e., strong correlation) were considered to represent biologically related or overlapping transcriptional programs rather than distinct regulatory units.

Using a cut height of 0.25 (eigengene correlation ≥ 0.75), the number of modules was reduced from 40 to 36.

<img width="700" height="432" alt="image" src="https://github.com/user-attachments/assets/de60be6f-6e74-4b73-b21b-78ed49d6d72f" />


As a result of this merging step:

40 modules were initially detected after dynamic tree cutting.

36 modules remained after merging highly correlated modules.



## 6. Hub gene identification 

Hub genes (isoforms) were identified for each module using the chooseTopHubInEachModule() function, which selects the gene with the highest intramodular connectivity.

Hub isoform of the cyan module:
The hub isoform corresponds to the transcript reported by chooseTopHubInEachModule() for the cyan module in the W dataset analysis output.
The hub gene of the cyan module are  __Bradi1g00700.3__, __Bradi1g38687.1__ , __Bradi4g44000.__, __Bradi3g51757.1__, __Bradi3g22980.2__ and __Bradi1g54250.1__ as mentionned in the following image. 

<img width="1001" height="423" alt="image" src="https://github.com/user-attachments/assets/58163631-2257-4990-ba2d-6c205f84a166" />



## 7. Module Trait Relationship 

In the last step we correlated the eigengenes withe the phenotypic traits using Pearson Correlation. 
The resulting module trait association heatmap revealed the strongest positive association with below-ground biomass.

![heatmap_traits_modules_W_page-0001](https://github.com/user-attachments/assets/03814b02-d7e4-47a7-81a9-a178d803a0bf)

According to the to the module-trait association heat map, Module with highest positive correlation with “blwgrd (below-ground biomass)” trait:
Violet module (correlation ≈ 0.66, p ≈ 3 × 10⁻⁴). 



# Conclusion 

The Weighted gene co-expression network analysis (WGCNA) of the RNA dataset of the watered samples identified robust co-expression modules after performing the quality control and the network refinement. Module merging reduced redundancy and improved biological interpretability , passing from 25,640 isoforms into 20 functional modules. While hub gene and module–trait analyses highlighted key modules associated with below-ground biomass. These results demonstrate the effectiveness of WGCNA for uncovering biologically meaningful transcriptional patterns in plant transcriptomic data.











