# PBMC 3k single-cell RNA-seq workflow (Scanpy)

A learning exercise: the standard single-cell RNA-seq workflow run end to end on the public PBMC 3k dataset, following the official Scanpy clustering tutorial. This is not original research.

## Data

Peripheral blood mononuclear cells from one healthy donor, released publicly by 10x Genomics. Loaded with `sc.datasets.pbmc3k()` as raw counts: 2,700 cells x 32,738 genes.

## Steps

1. **Quality control**: removed cells with fewer than 200 genes, 2,500 or more genes, or 5% or more mitochondrial counts; removed genes detected in fewer than 3 cells. Result: 2,638 cells x 13,714 genes.
2. **Normalization**: scaled each cell to 10,000 counts, then log(1 + x). Raw counts kept in `adata.layers["counts"]`.
3. **Feature selection**: top 2,000 highly variable genes.
4. **Dimensionality reduction**: PCA, neighbour graph, UMAP.
5. **Clustering**: Leiden algorithm, giving 9 clusters.
6. **Annotation**: known marker genes plus a Wilcoxon rank-sum test of each cluster against the rest.

## Result

| Cluster | Top markers | Cell type |
|---|---|---|
| 0 | CCL5, NKG7, GZMA, CD3D | Cytotoxic (CD8) T cells |
| 1 | Ribosomal genes (RPS12, RPS6, RPL32) | Naive CD4 T cells |
| 2 | GNLY, NKG7, GZMB, PRF1 | NK cells |
| 3 | CD74, CD79A, CD79B, MS4A1 | B cells |
| 4 | LTB, IL32, IL7R, CD3D | Memory CD4 T cells |
| 5 | LYZ, S100A9, S100A8 | Classical (CD14) monocytes |
| 6 | LST1, FCER1G, FCGR3A | Non-classical (CD16) monocytes |
| 7 | HLA-DPB1, HLA-DRA, CD74, CST3 | Dendritic cells |
| 8 | PPBP, PF4 | Platelets |

Cluster numbers depend on the software versions and may differ on a re-run.

## Limitations

- One donor, so no batch integration and no comparison between conditions.
- Cluster-vs-rest p-values are inflated: cells from one donor are not independent replicates, and the clusters were defined from the same data. They are used here only to rank markers.
- QC thresholds were chosen by eye for healthy blood and would not transfer directly to tumour tissue.
- The annotation of cluster 1 as naive CD4 T cells rests on its position among the T cells and its lack of distinctive markers, not on a specific marker.

## Reproduce

Python 3.13.16, Scanpy 1.12.4 (Windows).

```bash
py -m venv sc
.\sc\Scripts\python -m pip install -r requirements.txt
```

Then open `pbmc3k.ipynb`, select the `sc` environment as the kernel, and run all cells.
