# Data Leakage in Oral Cancer Image Classification

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Notebook validation](https://github.com/Prajwal-Pratap-Yadav/oral-cancer-data-leakage/actions/workflows/validate-notebook.yml/badge.svg)](https://github.com/Prajwal-Pratap-Yadav/oral-cancer-data-leakage/actions/workflows/validate-notebook.yml)

Research code accompanying the manuscript:

> **Data Leakage in Oral Cancer Image Classification: A Pre-Specified Matched-Arm Study With Patient-Level and External Validation**  
> Prajwal Pratap Yadav, Esha Pramanick, and Aleena Swetapadma  
> Submitted to the *IEEE Journal of Biomedical and Health Informatics* (submission status as stated in the notebook; update this citation when the publication record is available).

This repository studies how image-level splitting, duplicate or related images, and source/patient overlap can inflate apparent performance in oral-cancer image classification. It provides a matched-arm experimental pipeline, data audits, leakage-controlled split construction, training/evaluation procedures, and scripts for regenerating selected paper figures.

## Repository contents

| Path | Description |
|---|---|
| [`oralleak_all_blocks.ipynb`](oralleak_all_blocks.ipynb) | Combined 65-cell Stage 2 notebook: primary analysis, amendments A1/A2, post-hoc A2 addendum, and figure-generation scripts. |
| [`CITATION.cff`](CITATION.cff) | Machine-readable software citation metadata. |
| [`.github/workflows/validate-notebook.yml`](.github/workflows/validate-notebook.yml) | Lightweight CI check for notebook JSON/nbformat validity; it does not execute experiments. |
| [`LICENSE`](LICENSE) | MIT License for the original repository code. |
| [`.gitignore`](.gitignore) | Python/Jupyter ignore rules. |

### Important scope limitation

The notebook's Stage 1 exploratory replication (Blocks 1–14) is **not included**. The first notebook cells explicitly retain a placeholder for those blocks. The committed notebook starts with Stage 2, Blocks 15–41. Consequently, this repository does not yet contain every experiment described in the manuscript's full study history. Do not interpret the current notebook as a complete Stage 1 + Stage 2 reproduction.

The notebook also has no saved cell outputs. Result JSON files, tables, figures, run predictions, and release ZIP archives are generated during execution and are not currently stored in this repository.

## Study components

| Component | Notebook blocks | Main purpose |
|---|---:|---|
| Stage 2 primary analysis | 15–28 | Analysis-plan setup, data audit, frozen-feature probes, split planning, training, evaluation, statistics, and release packaging. |
| Amendment A1 | 29–33 | ORCHID oral H&E data, patient-level comparisons, metadata-free group recovery, Phikon feature analyses, and A1 results. |
| Amendment A2 | 34–40 | NDB-UFES author-release ingestion, patient-level grouping, grouping sensitivity, external validation, and A2 release packaging. |
| A2 addendum | 41 | Post-hoc, descriptive external AUROC/sensitivity/specificity analysis; no additional model training. |
| Figure scripts | End of notebook | Writes `fig3_own_vs_holdout.py` and `fig4_fig5_a2.py` and runs them against extracted release archives. |

The experimental pipeline includes matched leaky and leakage-controlled arms, source/patient-aware split construction, exact/near-duplicate audits, random-label and overlap controls, augmentation-dose analyses, multiple backbones, and cluster-aware uncertainty estimation. Consult the analysis-plan and amendment cells before interpreting any individual comparison.

## Data

The notebook expects the following public datasets to be attached through Kaggle **Add Input**, except for sources downloaded by the notebook itself.

| Dataset | Source / access |
|---|---|
| OCDC (H&E patches) | [Kaggle: he-stained](https://www.kaggle.com/datasets/esha76/he-stained) |
| Histopathology corpus of Rahman et al. | [Kaggle: dataset](https://www.kaggle.com/datasets/ashenafifasilkebede/dataset) |
| NDB-UFES redistributed copy | [Kaggle: ndb-ufes-dataset](https://www.kaggle.com/datasets/avijit0001/ndb-ufes-dataset) |
| Clinical photographs (lips and tongue) | [Kaggle: oral-cancer-lips-and-tongue-images](https://www.kaggle.com/datasets/shivam17299/oral-cancer-lips-and-tongue-images) |
| BreakHis | [Kaggle: breakhis](https://www.kaggle.com/datasets/ambarish/breakhis) |
| ORCHID oral H&E data | Downloaded by Block 30 from [Zenodo record 12646943](https://doi.org/10.5281/zenodo.12646943) and [Zenodo record 12636426](https://doi.org/10.5281/zenodo.12636426). |
| NDB-UFES author release | Downloaded by Block 35 from [Mendeley Data, version 4](https://doi.org/10.17632/bbmmm4wgr8.4). |

**Dataset terms are separate from this repository's MIT License.** Obtain each dataset from its authoritative source, review its current terms and attribution requirements, and do not redistribute images or derived files unless the dataset license permits it. This repository does not include the datasets.

## Execution environment

The notebook is designed for a **Kaggle Notebook** environment rather than a generic local Python run. Its documented execution assumptions are:

- Python 3.13 notebook kernel.
- Two NVIDIA T4 GPUs for the documented training workflow.
- Internet enabled for package/model downloads and the ORCHID/NDB-UFES downloads.
- Kaggle persistence configured to retain files under `/kaggle/working/q1/` between sessions.
- The work directory defaults to `/kaggle/working/q1`; set `Q1_ROOT` to override it.

Block 15 creates the shared `q1lib.py` library and installs selected missing packages (`timm==1.0.15`, `statsmodels`, `scikit-image`, and `psutil`). Block 34 may install `transformers`. The Kaggle image is expected to provide PyTorch and the CUDA runtime. There is currently no complete lockfile or fully pinned environment specification; package availability and GPU/runtime versions can affect reproducibility.

## Execution order

Run the notebook blocks in their documented order. The block-level markdown in the notebook is the authoritative protocol; the abbreviated sequences below are a navigation aid.

1. **Stage 2 initial session:** run Block 15, save a Kaggle version to timestamp the analysis plan, then run Blocks 16–22.
2. **Stage 2 training sessions:** in each new session run Blocks 15 and 19, then resume the first unfinished training phase among Blocks 20–24. Repeat the relevant launcher until it reports completion.
3. **Stage 2 final analysis:** run Blocks 15 and 25–28 after the planned training runs are complete.
4. **Amendment A1:** run Blocks 15, 19, and 29; save a Kaggle version; then run Blocks 30, 31, and 32 (resume Block 32 as needed), followed by Blocks 25, 27, 33, and 28.
5. **Amendment A2:** run Blocks 15, 19, 34, 35, and 36 once; run/resume Block 37 until complete; then run Blocks 38–40.
6. **A2 post-hoc addendum:** run Blocks 15, 34, and 41 after the A2 results are available.

### State and protocol cautions

- **Do not rerun Block 18 after Block 31 or Block 36.** Block 18 rebuilds `jobs.json` without the later A1/A2 jobs. If this happens, the notebook itself instructs you to rebuild the affected amendment jobs by rerunning Blocks 31 and 36.
- Training launchers are resumable. In a new Kaggle session, rerun Blocks 15 and 19 before resuming the relevant training phase.
- Running Blocks 15, 29, or 34 in a fresh environment may create new timestamps and hashes for the analysis plan/amendments. Those newly generated records are not a substitute for the original records used in the reported experiments.
- Do not run the full notebook top-to-bottom without following the block-specific prerequisites. Some blocks require prior artifacts in persistent storage and can take substantial GPU time.

## Outputs and figure regeneration

The notebook writes artifacts under `Q1_ROOT`, by default `/kaggle/working/q1/`. Depending on the blocks run, these include:

- Release archives under `q1/release/` (Blocks 28, 40, and 41).
- Run predictions under `q1/runs/*/preds.npz`.
- Evaluation JSON, CSV tables, audit manifests, split manifests, and figures.
- Two standalone figure scripts written by the final notebook cells.

The archives and run outputs are **not committed here**. Store them separately and preserve their checksums if they are part of a reproducibility package. The figure scripts expect the relevant release archives to be extracted under a common base directory; inspect each script's header for the required archive layout, then run:

```bash
python3 fig3_own_vs_holdout.py <BASE> <OUT>
python3 fig4_fig5_a2.py <BASE> <OUT>
```

## Validation

The GitHub Actions workflow validates that the notebook is structurally valid nbformat. It intentionally does **not** execute training, download datasets, or access GPUs. Full experimental reproduction requires the Kaggle environment, the required data inputs, persistent working files, and the correct execution order described above.

## Citation

If you use this code, cite the associated manuscript:

> P. P. Yadav, E. Pramanick, and A. Swetapadma, “Data Leakage in Oral Cancer Image Classification: A Pre-Specified Matched-Arm Study With Patient-Level and External Validation,” submitted to the *IEEE Journal of Biomedical and Health Informatics*.

The repository includes [`CITATION.cff`](CITATION.cff). Add the final DOI and publication metadata to both files when the manuscript is published.

## License and intended use

The original code in this repository is released under the [MIT License](LICENSE). The license does not extend to third-party datasets, pretrained weights, or dependencies.

This project is for research and methodological evaluation. It is **not** a clinically validated diagnostic system and must not be used for patient diagnosis or treatment decisions.
