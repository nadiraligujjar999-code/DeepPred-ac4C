# DeepPred-ac4C

**DeepPred-ac4C** is a hybrid deep learning framework for the identification of RNA **N4-acetylcytidine (ac4C)** modification sites from RNA sequence data. The framework combines sequence-level and handcrafted feature representations with convolutional, recurrent, and attention-based learning modules to improve ac4C site prediction.

This repository provides the implementation of the DeepPred-ac4C classifier model used in the accompanying manuscript:

**Integrative Multi-Scale Encoding and Attention-Driven Mechanism for N4-Acetylcytidine Modification Site Identification**

---

## Overview

N4-acetylcytidine (ac4C) is an important RNA modification involved in post-transcriptional gene regulation. Experimental identification of ac4C sites is often expensive, labor-intensive, and time-consuming. DeepPred-ac4C provides a computational approach for predicting ac4C sites using RNA sequence information.

The model integrates:

- **One-hot encoding** to preserve nucleotide order and positional sequence information.
- **k-mer descriptors** to capture local nucleotide composition and short-range dependencies.
- **Position-Specific Trinucleotide Propensity (PSTNP)** to represent discriminative positional trinucleotide patterns.
- **CNN layers** to extract local motifs.
- **BiLSTM layers** to learn long-range contextual dependencies.
- **Multi-head attention Transformer block** to capture global sequence relationships.
- **Feature fusion and sigmoid classification layer** for binary ac4C site prediction.

---

## Model Architecture

DeepPred-ac4C follows a dual-branch deep learning design.

The first branch processes one-hot encoded RNA sequences through a dense transformation, CNN encoder, BiLSTM layer, positional embedding, and multi-head attention block. This branch learns local sequence motifs, bidirectional contextual information, and global dependencies across the RNA sequence.

The second branch processes handcrafted descriptors, including k-mer and PSTNP features, through a lightweight CNN-based module. This branch learns descriptor-level interactions and compositional patterns.

The learned representations from both branches are concatenated and passed through dropout regularization and a sigmoid-activated dense layer for binary classification.

---

## Dataset

The benchmark dataset contains cytidine-centered RNA sequence fragments of length **201 nucleotides**, constructed using 100 nucleotides upstream and 100 nucleotides downstream of the central cytidine.

After redundancy reduction using **CD-HIT with 0.8 sequence identity cutoff**, the final balanced dataset contains:

| Class | Number of Samples |
|---|---:|
| Positive ac4C samples | 2758 |
| Negative non-ac4C samples | 2758 |
| Total samples | 5516 |

The dataset was divided into training and independent test subsets using an **80:20 split ratio**.

---

## Feature Representations

The following feature representations are used in DeepPred-ac4C:

| Feature | Description | Dimension |
|---|---|---:|
| One-hot | Position-wise RNA nucleotide encoding | 804 |
| k1 | Mono-nucleotide frequency descriptor | 4 |
| k2 | Di-nucleotide frequency descriptor | 16 |
| k3 | Tri-nucleotide frequency descriptor | 64 |
| PSTNP | Position-specific trinucleotide propensity descriptor | 199 |

The best-performing final feature configuration used in DeepPred-ac4C is:

```text
onehot + k1 + k2 + PSTNP
```

---

## Experimental Settings

DeepPred-ac4C was evaluated using:

- Independent test set evaluation
- Stratified 10-fold cross-validation
- Feature ablation analysis
- Comparative analysis with existing ac4C prediction methods

The main evaluation metrics include:

- Accuracy (ACC)
- Sensitivity (SN)
- Specificity (SP)
- Matthews Correlation Coefficient (MCC)
- ROC-AUC
- PR-AUC

The experiments were implemented in Python using TensorFlow/Keras and scikit-learn. The experiments were conducted in the Kaggle environment with NVIDIA Tesla P100 GPU support.

---

## Main Results

### Independent Test Performance

Using the final feature set **onehot + k1 + k2 + PSTNP**, DeepPred-ac4C achieved the following performance on the independent test set:

| Metric | Score |
|---|---:|
| ACC | 0.8451 |
| MCC | 0.6903 |
| SN | 0.8514 |
| SP | 0.8388 |
| ROC-AUC | 0.9065 |
| PR-AUC | 0.8799 |

### 10-Fold Cross-Validation Performance

| Metric | Mean ± Std |
|---|---:|
| ACC | 0.8155 ± 0.0221 |
| MCC | 0.6335 ± 0.0431 |
| SP | 0.8006 ± 0.0372 |
| SN | 0.8305 ± 0.0599 |

---

## Ablation Study

The feature ablation study confirmed that integrated feature representations perform better than most individual descriptors. The final feature set **F14 (onehot + k1 + k2 + PSTNP)** achieved the most balanced performance with the highest accuracy and MCC.

| Feature Set | Feature Combination | ACC | MCC | SN | SP |
|---|---|---:|---:|---:|---:|
| F1 | onehot | 0.8379 | 0.6771 | 0.8696 | 0.8062 |
| F2 | k1 | 0.7600 | 0.5223 | 0.8080 | 0.7120 |
| F3 | k2 | 0.7726 | 0.5456 | 0.7899 | 0.7554 |
| F4 | k3 | 0.7772 | 0.5551 | 0.8025 | 0.7518 |
| F5 | PSTNP | 0.7129 | 0.4262 | 0.6902 | 0.7355 |
| F6 | onehot + PSTNP | 0.8089 | 0.6181 | 0.7917 | 0.8261 |
| F7 | onehot + k1 | 0.8388 | 0.6778 | 0.8514 | 0.8261 |
| F8 | k1 + k2 + k3 | 0.7926 | 0.5857 | 0.8134 | 0.7717 |
| F9 | k1 + k2 + PSTNP | 0.7889 | 0.5791 | 0.8207 | 0.7572 |
| F10 | k1 + k2 + k3 + PSTNP | 0.7853 | 0.5721 | 0.8207 | 0.7500 |
| F11 | onehot + k1 + PSTNP | 0.8342 | 0.6694 | 0.8605 | 0.8080 |
| F12 | onehot + k1 + k2 | 0.8406 | 0.6823 | 0.8696 | 0.8116 |
| F13 | onehot + k1 + k2 + k3 | 0.8370 | 0.6749 | 0.8098 | 0.8641 |
| F14 | onehot + k1 + k2 + PSTNP | 0.8451 | 0.6903 | 0.8514 | 0.8388 |
| F15 | onehot + k1 + k2 + k3 + PSTNP | 0.8342 | 0.6694 | 0.8605 | 0.8080 |

---

## Repository Structure

A suggested repository structure is shown below:

```text
DeepPred-ac4C/
│
├── README.md
├── deeppred-ac4c--Classifier model.ipynb
├── trainset_labeled.txt
├── testset_labeled.fasta
└── diRNAPhyche.txt
```

---

## Installation

Clone the repository:

```bash
git clone https://github.com/nadiraligujjar999-code/DeepPred-ac4C.git
cd DeepPred-ac4C
```

Install the required Python packages:

```bash
pip install numpy pandas scikit-learn tensorflow matplotlib seaborn
```

For Kaggle execution, install a compatible protobuf version before importing TensorFlow:

```bash
pip install "protobuf<6"
```

---

## Usage

Open the notebook:

```text
deeppred-ac4c--Classifier model.ipynb
```

Update the dataset paths according to your environment:

```python
TRAIN_PATH = "path/to/trainset_labeled.txt"
TEST_PATH = "path/to/testset_labeled.fasta"
DINUC_PROP_PATH = "path/to/diRNAPhyche.txt"
```

Run the notebook cells in order to:

1. Load the labeled RNA sequence data.
2. Generate one-hot, k-mer, and PSTNP features.
3. Build the DeepPred-ac4C model.
4. Train the model.
5. Evaluate it on the independent test set.
6. Save the ablation result table.

The final ablation results are saved as:

```text
ablation_independent_results.csv
```

---

## Input Data Format

The notebook expects labeled FASTA-style input files. Each sequence header should contain the class label as `0` or `1`.

Example:

```text
>sample_1 1
ACGUACGUACGU...
>sample_2 0
UGCAUGCAUGCA...
```

Where:

- `1` represents an ac4C site.
- `0` represents a non-ac4C site.

---

## Output Files

The notebook can generate:

- Independent test result table
- Ablation result CSV file
- ROC-AUC curves
- PR-AUC curves
- Trained model objects during runtime

---

## Citation

If you use this repository, please cite the related manuscript:

```text
Nadir Ali and Muhammad Attique. Integrative Multi-Scale Encoding and Attention-Driven Mechanism for N4-Acetylcytidine Modification Site Identification. DeepPred-ac4C.
```

A complete citation will be added after publication.

---

## Contact

For questions, issues, or collaboration, please contact the repository maintainer through GitHub:

```text
https://github.com/nadiraligujjar999-code/DeepPred-ac4C
```

---

## License

Please add a suitable license file before public release, such as MIT, Apache-2.0, or another license required by your institution or journal.
