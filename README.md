# Atypical Mitotic Figure Classification

Soft, simple, and focused: this project builds a deep learning pipeline to classify mitotic figure patches into:

- AMF: Atypical Mitotic Figures
- NMF: Normal Mitotic Figures

AMF detection matters clinically, but atypical examples are rare and visually subtle in H&E histopathology images. This repository tackles that with careful augmentation, strong baseline benchmarking, and attention-based improvements.

## At A Glance

- 14,000+ RGB patches (224 x 224)
- Targeted AMF-only augmentation for class balance
- Baseline comparison across multiple architectures
- DenseNet-121 selected as the core backbone for downstream attention experiments
- Best attention result in this project: SE (Squeeze-and-Excitation)

## Repository Map

| Folder | What it contains |
|---|---|
| `Augmentation/` | Data augmentation and split preparation notebooks |
| `BaselineModel/` | Baseline model notebooks and result files |
| `Attentions/` | Attention experiments (SE, CBAM, CCA, ECA, PSA, etc.) |
| `Position/SE1D/` | SE1D placement and position-search experiments |
| `Report&PPT/` | Final report and presentation materials |

## Dataset Summary

### Data Sources

- MIDOG25
- AMI-BR
- MIDOG21 (via AMI-BR)
- TUPAC16 (via AMI-BR)

All samples are image patches centered on mitotic figures.

### Split Overview

| Split | Approx. samples | Notes |
|---|---:|---|
| Training | ~9,000 | Includes augmented AMF samples |
| Validation | ~3,000 | Original images only |
| Test | ~3,000 | Original images only |

Validation and test sets stay non-augmented to keep evaluation unbiased.

### Dataset Links

- Curated dataset: [Zenodo](https://zenodo.org/records/15188326)
- Augmented training set: [Kaggle](https://www.kaggle.com/datasets/lostluinor/mitoticfigure-spiltandaugmenteddataset)

## Class-Balance Strategy

AMF is underrepresented, so augmentation is applied only to AMF training images:

- 4 additional variants per AMF image
- Approximately +6,000 AMF samples
- Final train distribution near 1:1 (AMF:NMF)

Augmentation includes controlled rotation, flips, brightness/contrast shifts, and sharpness adjustment.

## Modeling Pipeline

1. Train and compare baseline architectures.
2. Select a robust backbone using accuracy, precision, recall, F1-score, and loss.
3. Add attention modules and compare performance.
4. Refine placement using SE1D position experiments.

## Baseline Results

| Model | Accuracy (%) | Loss | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|---:|
| EfficientNet-B0 | 85.29 | 0.1035 | 0.8500 | 0.8500 | 0.8500 |
| DenseNet-121 | 89.02 | 0.0900 | 0.8904 | 0.8902 | 0.8903 |
| ViT-B16 | 87.16 | 0.0960 | 0.8720 | 0.8715 | 0.8718 |
| ConvNeXt-Tiny | 89.31 | 0.0866 | 0.8890 | 0.8931 | 0.8906 |
| MobileNetV2-100 | 87.19 | 0.0976 | 0.8543 | 0.8690 | 0.8575 |
| NASNet-Large | 82.42 | 0.1471 | 0.7308 | 0.8242 | 0.7679 |
| RegNetY-16 | 87.16 | 0.0960 | 0.8720 | 0.8715 | 0.8718 |
| XceptionNet | 89.31 | 0.0866 | 0.8890 | 0.8931 | 0.8906 |

DenseNet-121 is used as the main backbone in later attention-focused experiments.

## Training Setup

| Parameter | Value |
|---|---|
| Input size | 224 x 224 |
| Batch size | 32 |
| Epochs | 50 |
| Optimizer | Adam |
| Loss | Focal Loss ($\alpha=0.75$, $\gamma=2.0$) |
| Output activation | Softmax |
| Backbone | DenseNet-121 (ImageNet pretrained) |
| Attention stack | SE + SE1D |

Precision is prioritized to reduce false positives in atypical mitosis screening.

## Attention Results

| Attention model | Accuracy | Loss | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|---:|
| CBAM | 0.8859 | 0.0874 | 0.8855 | 0.8859 | 0.8857 |
| CCA | 0.8888 | 0.0881 | 0.8833 | 0.8888 | 0.8854 |
| ECA | 0.8841 | 0.0986 | 0.8849 | 0.8841 | 0.8845 |
| GAM | 0.8741 | 0.0912 | 0.8810 | 0.8741 | 0.8771 |
| PSA | 0.8910 | 0.0885 | 0.8817 | 0.8877 | 0.8840 |
| **SE** | **0.8942** | **0.0874** | **0.8937** | **0.8942** | **0.8939** |
| SE+CCA | 0.8856 | 0.0941 | 0.8856 | 0.8856 | 0.8866 |
| SE+PSA | 0.8841 | 0.0982 | 0.8901 | 0.8841 | 0.8867 |
| Self-Att | 0.8795 | 0.0875 | 0.8782 | 0.8795 | 0.8788 |
| Triplet | 0.8802 | 0.0908 | 0.8849 | 0.8802 | 0.8823 |

SE gives the strongest overall result in this set of experiments.

## Credits

This work uses resources from the [MIDOG 2025 Challenge](https://midog2025.deepmicroscopy.org/) and related datasets.

Thanks to the MIDOG organizers and dataset contributors for supporting reproducible computational pathology research.