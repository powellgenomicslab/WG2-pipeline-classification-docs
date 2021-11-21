# General

The cell-type annotation framework implemented in this project consists on supervised classification using two approaches:

-   **Azimuth**: This method implemented in Seurat V4 classifies by cells by mapping the query dataset onto a supervised PCA space constructed from modal-weighted neighbours calculated by combining RNA and protein data.

-   **Hierarchical scPred:** This approach is based on two methods:

    -   ***scPred*****:** a supervised classification method that learns a low dimensional representation of a reference dataset. Unlike azimuth (which relies on a lazy classifier), *scPred* trains binary classifiers for each cell type using a *one vs. all* approach. The query dataset is projected onto the training low dimensional space and labels are assigned according to probability scores. For more details see <https://genomebiology.biomedcentral.com/articles/10.1186/s13059-019-1862-5>

    -   **Hierarchical progressive learning (scHPL)**: this method decomposes the classification problem into smaller sub-tasks according to a hierarchy representing cell-type relationships. Cells are classified progressively using a top-bottom approach instead of using a flat classifier. For more details see <https://www.nature.com/articles/s41467-021-23196-8>

The cell-type classification is guided using a [CITE-seq reference](https://www.cell.com/cell/fulltext/S0092-8674(21)00583-3) containing:

-   161,764 human PBMCs
-   20,953 genes
-   224 protein markers (CITE-seq)
-   31 cell types

| **Lymphoid populations**                                      | **Myeloid populations** |
|---------------------------------------------------------------|-------------------------|
| CD4 Naive, CD4 TCM, CD4 TEM, CD4 CTL, Treg, CD4 Proliferating | CD14 Mono, CD16 Mono    |
| CD8 Naive, CD8 TCM, CD8 TEM, CD8 Proliferating                | cDC1, cDC2, pDC, ASDC   |
| gdT, MAIT, ILC, dnT                                           | HSPC                    |
| NK, NK_CD56bright, NK Proliferating                           | Platelet, Eryth         |
| B naive, B intermediate, B memory, Plasmablast                | Doublet                 |

> "This PBMC reference dataset was generated as part of the Hao and Hao
> et al, Cell 2021 paper. It is comprised of data from eight volunteers
> enrolled in an HIV vaccine trial from which three time point samples
> were taken at day 0, 3, and 7 following vaccination. All 24 samples
> were processed with a CITE-seq panel of 228 TotalSeq A antibodies to
> generate single-cell RNA and ADT data. The data were then integrated
> using metholody described in the pre-print linked above to generate a
> weighted nearest neighbor (WNN) representation of the RNA and protein
> data jointly. This WNN representation is used in the Azimuth app to
> assign celltypes, embed in the reference UMAP, and impute protein
> levels for the query dataset". *Hao Y, et al. Cell. (2021)*
