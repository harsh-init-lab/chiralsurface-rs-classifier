# Chiral Crystal R/S Classification Using Transfer Learning

Reproducible Jupyter notebooks for binary classification of crystal chirality
as **R** or **S** using transfer learning with a pretrained ResNet-18 model. 
The pre-print for this work is available here: https://arxiv.org/abs/2606.13144

The project compares two image modalities:

1. **Fermi-surface images** generated from synthetic ARPES-like data.
2. **Real-space images** of rendered molecular or crystal structures.

The training pipeline uses two stages:

1. Training the classification head with the ResNet-18 backbone frozen.
2. Fine-tuning the complete network.

The datasets are archived on Zenodo, while this repository contains the
notebooks and environment specification required to reproduce the analysis.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21923938.svg)](https://doi.org/10.5281/zenodo.21923938)

---

## Scientific overview

The goal is to investigate whether deep transfer-learning models can classify
the surface chirality of crystal structures from image representations in
Fermi-surface and real-space modalities.

The Fermi-surface pipeline includes physics-informed image processing such as:

- Grayscale conversion.
- Resizing.
- Circular masking.
- Adaptive thresholding.
- Morphological preprocessing and arc thinning.
- Rotation and label-aware mirror-flip augmentation.

The real-space pipeline uses RGB images with:

- Resizing.
- ImageNet normalization.
- Colour augmentation.
- Affine transformations.
- Label-aware mirror-flip augmentation.
- Random erasing.

Mirror transformations are handled according to the chirality convention used
to construct the dataset. Please consult the notebooks and dataset metadata for
the precise transformation and label definitions.

---

## Dataset

The datasets are available from Zenodo:

**Dataset DOI:** [10.5281/zenodo.21923938](https://doi.org/10.5281/zenodo.21923938)

The dataset is distributed as two compressed archives:

- `fermi_surfaces.tar.gz` – Fermi-surface image dataset.
- `realspace.tar.gz` – Real-space image dataset.

The dataset is **not** included in this GitHub repository. Download the archives
from Zenodo and extract them into the repository root.

### Download and extraction

From the repository root, download and extract the archives, for example:

```bash
# Fermi-surface dataset
wget [https://zenodo.org/records/21923939/files/fermi_surfaces.tar.gz?download=1](https://zenodo.org/records/21923939/files/fermi_surfaces.tar.gz?download=1) -O fermi_surfaces.tar.gz
mkdir -p dataset
tar -xzf fermi_surfaces.tar.gz -C dataset
rm fermi_surfaces.tar.gz

# Real-space dataset
wget [https://zenodo.org/records/21923939/files/realspace.tar.gz?download=1](https://zenodo.org/records/21923939/files/realspace.tar.gz?download=1) -O realspace.tar.gz
tar -xzf realspace.tar.gz -C dataset
rm realspace.tar.gz
```

Adjust the commands if you prefer to download via the browser or use a different
download tool.

After extraction, the expected directory structure is:

```text
dataset/
├── fermi_surfaces/
│   ├── train/
│   │   ├── R/
│   │   └── S/
│   ├── val/
│   │   ├── R/
│   │   └── S/
│   └── test/
│       ├── R/
│       └── S/
└── realspace/
    ├── train/
    │   ├── R/
    │   └── S/
    ├── val/
    │   ├── R/
    │   └── S/
    └── test/
        ├── R/
        └── S/
```

The notebooks assume the following paths:

```text
dataset/fermi_surfaces
dataset/realspace
```

The labels are:

```text
R → class 0
S → class 1
```

Use the Zenodo record as the authoritative source for the dataset version,
metadata, license, and citation information.

---

## Repository contents

```text
.
├── README.md
├── requirements.yml
├── fermi_surface_rs_classifier.ipynb
├── fermi_surface_classifier_performance.ipynb
├── real_space_rs_classifier.ipynb
├── real_space_classifier_performance.ipynb
├── LICENSE
└── .gitignore
```

### Notebook descriptions

| Notebook | Purpose |
|---|---|
| `fermi_surface_rs_classifier.ipynb` | Train the R/S classifier using Fermi-surface images |
| `fermi_surface_classifier_performance.ipynb` | Evaluate the Fermi-surface classifier |
| `real_space_rs_classifier.ipynb` | Train the R/S classifier using real-space RGB images |
| `real_space_classifier_performance.ipynb` | Evaluate the real-space classifier |

---

## Installation

The recommended environment is specified in `requirements.yml`.

### Using Conda

```bash
conda env create -f requirements.yml
conda activate chiral-classifier
```

Start JupyterLab from the repository root:

```bash
jupyter lab
```

Alternatively:

```bash
jupyter notebook
```

### Hardware support

The notebooks select an available device in the following order:

1. NVIDIA CUDA.
2. Apple Silicon MPS.
3. CPU.

The first execution may download pretrained ResNet-18 ImageNet weights. Internet
access may therefore be required during the first run.

For NVIDIA systems, install the PyTorch build appropriate for your CUDA version
if the pinned environment does not provide the desired CUDA configuration.

---

## Running the notebooks

Run the notebooks from the repository root after downloading and extracting the
dataset.

### Fermi-surface training

Open:

```text
fermi_surface_rs_classifier.ipynb
```

Run the cells from top to bottom. The notebook:

- Loads the Fermi-surface dataset.
- Applies preprocessing and augmentation.
- Constructs train, validation, and test data loaders.
- Initializes a pretrained ResNet-18 model.
- Trains the classification head.
- Fine-tunes the complete model.
- Saves the best checkpoint as:

```text
fermi_surface_rs_classifier.pth
```

### Fermi-surface evaluation

Open:

```text
fermi_surface_classifier_performance.ipynb
```

This notebook loads the Fermi-surface checkpoint and evaluates the model on the
validation and test sets.

It reports:

- Accuracy.
- Precision.
- Recall.
- F1 score.
- Confusion matrix.
- ROC curve.
- AUC-ROC.

### Real-space training

Open:

```text
real_space_rs_classifier.ipynb
```

Run the cells from top to bottom. The notebook trains a ResNet-18 classifier
using the real-space RGB images and saves the best checkpoint as:

```text
real_space_rs_classifier.pth
```

### Real-space evaluation

Open:

```text
real_space_classifier_performance.ipynb
```

This notebook loads the real-space checkpoint and evaluates its classification
performance on the validation and test sets.

---

## Reproducibility

To reproduce the analysis:

1. Clone this repository.
2. Create the Conda environment using `requirements.yml`.
3. Download the dataset from Zenodo.
4. Extract the dataset into the repository root.
5. Confirm that `dataset/fermi_surfaces` and `dataset/realspace` exist.
6. Start JupyterLab from the repository root.
7. Run each notebook from top to bottom.
8. Record the Python, PyTorch, torchvision, OpenCV, and hardware versions.

The notebooks set random seeds where appropriate, but exact results may vary
because of hardware, software versions, pretrained-weight availability, and
nondeterministic GPU operations.

The reported metrics should always be interpreted together with:

- Dataset DOI and version.
- Train/validation/test split.
- Model checkpoint.
- Random seed.
- Preprocessing configuration.
- Hardware and software versions.

---

## Generated files

The training notebooks may generate:

```text
fermi_surface_rs_classifier.pth
real_space_rs_classifier.pth
outputs/
```

These files are excluded from the default Git history by `.gitignore`.

If a particular checkpoint or figure is needed for citation, archive it
explicitly in Zenodo or another appropriate research repository and document its
relationship to the corresponding GitHub release.

---

## License

**Code and notebooks**

Copyright (C) 2026 C Badala Viswanatha, Ka Man Yu, Benito Arnoldi,Anagha Aravind, Aaruni Kaushik, Jannis Lessmeister, Martin Aeschlimann, Benjamin Stadtmueller, and S. Harshini Tekur.

The code and Jupyter notebooks in this repository are licensed under the
[GNU General Public License v3.0](LICENSE.txt).

**Datasets**

All image datasets are deposited on Zenodo under the
Creative Commons Attribution 4.0 International (CC-BY 4.0) license:

- Dataset DOI: [10.5281/zenodo.21923938](https://doi.org/10.5281/zenodo.21923938)

The GPL-3.0 license applies to the code and notebooks only. The CC-BY 4.0
license applies to the Zenodo-hosted image datasets.
