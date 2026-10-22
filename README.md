# 🧬 Multi-omics hands-on workshop — integrating proteomics and metabolomics

**Danish Bioinformatics Conference 2026 · Odense · 27–28 October 2026 · 2 hours**

One real clinical cohort, two omics layers measured on the same 45 serum samples, and two
Google Colab notebooks that integrate them: first at the level of *patients* (Similarity
Network Fusion, MOFA), then at the level of *molecules* (cross-omics networks, joint
pathway analysis, the methionine cycle).

You need a Google account and a browser. Nothing to install.

This workshop is a two-hour condensation of the three-day course
[**Multi-omics Data Analysis**](https://github.com/Multiomics-Analytics-Group/course_multi-omics_analysis).
Raw-data processing (nf-core pipelines) and the single-omics analyses are covered in the
introductory slides; the full course has the notebooks for them.

---

## 📅 Programme

| Time | Session | Material |
|---|---|---|
| 0:00–0:25 | Introduction: dataset, nf-core processing, single-omics results, integration basics | [slides](slides/01_workshop_introduction.pptx) |
| 0:25–0:35 | Multi-omics | [slides](slides/02_multiomics_lecture.pptx) |
| 0:35–1:10 | **Hands-on I — Integration (SNF, MOFA)** | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/biosustain/dsp_multiomics_workshop/blob/main/notebooks/01_multiomics_integration.ipynb) |
| 1:10–1:20 | Introduction to Network Biology | [slides](slides/03_network_biology_lecture.pptx) |
| 1:20–1:55 | **Hands-on II — Networks and pathways** | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/biosustain/dsp_multiomics_workshop/blob/main/notebooks/02_multiomics_networks.ipynb) |
| 1:55–2:00 | Wrap up | |

> 💡 Open both notebooks at the start and choose **Runtime → Run all** once, so the package
> installs are done before you need them. Each notebook stands on its own: it downloads the
> tables it needs from this repository.

### Questions to answer during the hands-on

**Multi-omics I — Integration**
1. Is the Mantel correlation between the two layers positive? Is the signal shared or complementary?
2. Do the SNF clusters match the clinical groups — or a clinical laboratory value?
3. Which MOFA factor tracks group, and which tracks CRP, procalcitonin or creatinine?

**Multi-omics II — Networks and pathways**
1. How many protein–metabolite edges survive FDR 5 %, and how many FDR 20 %?
2. Who are the hubs — metabolic controllers, or markers of a global state?
3. Does MAT2B correlate with S-adenosylmethionine (SAM)? Would you believe it?

---

## 🧫 The dataset

> **He J, Luo S, Xu W, Chen Y, Liu G, Tang J, Yang Y, Zhao B, Ma L, Sheng H, Mao E.**
> *Serum proteomic profiling of sepsis patients reveals a protein-based diagnostic model,
> with metabolomic insights into carbapenem-resistant Klebsiella pneumoniae infection.*
> **Front Immunol. 2026;17:1818068.** [doi:10.3389/fimmu.2026.1818068](https://doi.org/10.3389/fimmu.2026.1818068) · open access, CC BY 4.0

Sepsis caused by **carbapenem-resistant *Klebsiella pneumoniae* (CRKP)** kills roughly
20–40 % of patients, about twice the rate of a susceptible infection, but culture and
susceptibility testing take one to three days. The study asks whether the patient's own
serum molecules can tell a resistant from a susceptible infection on day 0.

| Group | n | Description | Sample IDs |
|---|---|---|---|
| **Con** | 15 | Sepsis, all microbiological cultures negative | `Con1`–`Con15` |
| **CSKP** | 15 | Sepsis with carbapenem-**susceptible** *K. pneumoniae* | `KP1`–`KP15` |
| **CRKP** | 15 | Sepsis with carbapenem-**resistant** *K. pneumoniae* | `CRKP1`–`CRKP15` |

| | Proteomics | Metabolomics |
|---|---|---|
| Repository | [PXD075261](https://www.iprox.cn//page/SCV017.html?query=PXD075261) (ProteomeXchange / iProX) | [MTBLS14016](https://www.ebi.ac.uk/metabolights/MTBLS14016) (MetaboLights) |
| Instrument | timsTOF Pro (Bruker) | QTRAP 6500 (SCIEX) |
| Acquisition | diaPASEF, data-independent | MRM, widely targeted, ± ionisation |
| Processing | DIA-NN 1.9.2, library-free, MaxLFQ | vendor MRM peak integration |
| Features | 1 458 protein groups | 1 073 named metabolites |
| Pooled QC injections | 3 | 6 |

⚠️ **Acquisition order is confounded with group**: all Con samples were injected first, then
all CSKP, then all CRKP. Instrument drift is therefore indistinguishable from biology. Keep
this in mind whenever a result looks too good — the extended networks notebook explores it.

### Files in `data/`

Curated, tidy tables built from the public deposits by the
[full course](https://github.com/Multiomics-Analytics-Group/course_multi-omics_analysis/blob/main/material/datasets.md)
(`bin/build_curated_data.py`, `bin/build_sdrf.py`).

| File | Contents | Used by |
|---|---|---|
| `protein_groups_matrix.tsv` | 1 458 protein groups × 48 runs (45 patients + 3 QC), MaxLFQ intensities, with gene and description columns | both notebooks |
| `metabolite_matrix.tsv` | 1 073 metabolites × 51 injections (45 patients + 6 QC), MRM peak areas | both notebooks |
| `metabolite_annotation.tsv` | compound class, formula, HMDB, ChEBI, **KEGG compound and pathway** for 415 metabolites | both notebooks |
| `sample_metadata.tsv` | 45 patients × 28 columns: group, sex, age, BMI, 15 laboratory values, comorbidities | both notebooks |
| `published_integrated_pathways.tsv` | the paper's joint proteome + metabolome pathway analysis (3 pathways) | Multi-omics II |
| `PXD075261.sdrf.tsv` | SDRF metadata for the proteomics runs; the notebook reads injection order from it | Multi-omics II, extended version |

Sample identifiers are already harmonised across the two layers (the DIA-NN output originally
wrote `CPKP` for `CRKP`). The notebooks still assert that both layers describe the same 45
patients before integrating — a habit worth keeping.

---

## 📁 Repository contents

```
├── slides/
│   ├── 01_workshop_introduction.pptx   dataset, nf-core processing, single-omics results, integration basics
│   ├── 02_multiomics_lecture.pptx      Multi-omics (A. Santos, Y. Cuesta-Astroz)
│   └── 03_network_biology_lecture.pptx Introduction to Network Biology (A. Santos, Y. Cuesta-Astroz)
├── notebooks/
│   ├── 01_multiomics_integration.ipynb
│   ├── 02_multiomics_networks.ipynb
│   └── 02_multiomics_networks_with_AUC.ipynb
└── data/                               the six input tables described above
```

### Running locally instead of Colab

```bash
git clone https://github.com/biosustain/dsp_multiomics_workshop.git
cd dsp_multiomics_workshop
pip install jupyter pandas numpy scipy scikit-learn matplotlib seaborn networkx snfpy mofapy2 mofax
jupyter lab
```

Multi-omics II downloads KEGG mappings live from <https://rest.kegg.jp>; without network
access it falls back to the published pathway table. The Cytoscape export needs the
[Cytoscape](https://cytoscape.org/) desktop application only if you want to open the files.

---

## 📚 References

1. He J, *et al.* [Serum proteomic profiling of sepsis patients reveals a protein-based diagnostic model, with metabolomic insights into carbapenem-resistant Klebsiella pneumoniae infection](https://doi.org/10.3389/fimmu.2026.1818068). *Front Immunol.* 2026;17:1818068.
2. Langer BE, *et al.* [Empowering bioinformatics communities with Nextflow and nf-core](https://pubmed.ncbi.nlm.nih.gov/40731283/). *Nat Methods.* 2025.
3. Dai C, *et al.* [quantms: a cloud-based pipeline for quantitative proteomics enables the reanalysis of public proteomics data](https://www.nature.com/articles/s41592-024-02343-1). *Nat Methods.* 2024;21:1603–1607.
4. Wang B, *et al.* [Similarity network fusion for aggregating data types on a genomic scale](https://www.nature.com/articles/nmeth.2810). *Nat Methods.* 2014;11:333–337.
5. Argelaguet R, *et al.* [Multi-Omics Factor Analysis — a framework for unsupervised integration of multi-omics data sets](https://www.embopress.org/doi/full/10.15252/msb.20178124). *Mol Syst Biol.* 2018;14:e8124.
6. Cantini L, *et al.* [Benchmarking joint multi-omics dimensionality reduction approaches for the study of cancer](https://www.nature.com/articles/s41467-020-20430-7). *Nat Commun.* 2021;12:124.
7. Shannon P, *et al.* [Cytoscape: a software environment for integrated models of biomolecular interaction networks](https://pubmed.ncbi.nlm.nih.gov/14597658/). *Genome Res.* 2003;13:2498–2504.
8. Kanehisa M, *et al.* [KEGG: integrating viruses and cellular organisms](https://pubmed.ncbi.nlm.nih.gov/33125081/). *Nucleic Acids Res.* 2021;49:D545–D551.

## Acknowledgements and licence

Material adapted from the course
[Multi-omics Data Analysis](https://github.com/Multiomics-Analytics-Group/course_multi-omics_analysis)
of the Multiomics Analytics Group (DTU Biosustain). We thank *He et al.* for depositing both
omics layers of their cohort publicly.

Code and notebooks: Apache License 2.0 (see [LICENSE](LICENSE)). Data: derived from the
public deposits PXD075261 and MTBLS14016 and the open-access paper (CC BY 4.0); cite the
original study when reusing them. Lecture slides may contain third-party figures that keep
their original licences.
