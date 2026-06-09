# Automated Skeletal Classification and ANB Angle Prediction from Lateral Cephalograms

## Overview

This repository contains a deep learning framework for automated sagittal skeletal classification and ANB angle prediction directly from lateral cephalometric radiographs.

The proposed model performs:

* Skeletal Class I / II / III classification
* Continuous ANB angle regression
* End-to-end inference from raw cephalograms
* No landmark annotation required during inference

The framework employs a multi-input, multi-task learning architecture combining raw cephalograms and angle-enhanced representations during training, while using only raw images during deployment.

---


### Performance

Test Set Results:

| Metric                  | Value  |
| ----------------------- | ------ |
| Classification Accuracy | 87.92% |
| Weighted Precision      | 88.86% |
| Weighted Recall         | 87.92% |
| Weighted F1 Score       | 86.46% |
| ANB MAE                 | 1.109° |
| ANB RMSE                | 1.578° |
| ANB R²                  | 0.731  |

---

## Dataset

This project was developed using the **Aariz Cephalometric Dataset**.

Dataset DOI:

https://doi.org/10.6084/m9.figshare.27986417

The dataset contains:

* 1,000 lateral cephalometric radiographs
* Images acquired using multiple X-ray systems
* Expert-annotated cephalometric landmarks
* ANB measurements
* Skeletal Class I / II / III labels

### Dataset Split

The original dataset split was preserved:

| Cohort     | Samples |
| ---------- | ------- |
| Training   | 700     |
| Validation | 150     |
| Testing    | 150     |

---

## Model Architecture

### Backbone Networks

#### Raw Image Branch

* ConvNeXt-Large
* ImageNet pretrained

#### Angle-Enhanced Branch

* EfficientNet-B3
* ImageNet pretrained

### Feature Fusion

The framework integrates:

* Spatial Attention
* Channel Attention
* Feature Concatenation
* Multi-layer Fusion Network

### Multi-Task Outputs

#### Classification Head

Predicts:

* Class I (ANB 0–4°)
* Class II (ANB >4°)
* Class III (ANB <0°)

#### Regression Head

Predicts:

* Continuous ANB angle

---

## Training Strategy

### Data Augmentation

Albumentations-based augmentations:

* Resize
* Rotation
* Scaling
* Translation
* Horizontal Flip
* CLAHE
* Histogram Equalization
* Brightness/Contrast Adjustment
* Noise Injection
* Blur Augmentation

### Optimization

* AdamW Optimizer
* CosineAnnealingWarmRestarts
* Focal Loss (classification)
* Smooth L1 Loss (ANB regression)
* Mixed Precision Training
* Gradient Clipping
* Stochastic Weight Averaging (SWA)
* Test-Time Augmentation (TTA)

---

## Directory Structure

```text
Project/
│
├── Train_Cephalograms/
├── Validation_Cephalograms/
├── Test_Cephalograms/
│
├── angles/
│   ├── image1.png
│   ├── image2.png
│   └── ...
│
├── Train_set.xlsx
├── Val_list.xlsx
├── Test_Ceph.xlsx
│
└── optimized_outputs/
```

---

## Excel File Requirements

Each spreadsheet must contain:

| Column         | Description                |
| -------------- | -------------------------- |
| Image ID       | Image filename             |
| ap class clean | Skeletal class label (1–3) |
| ANB            | Ground-truth ANB angle     |

Example:

| Image ID | ap class clean | ANB  |
| -------- | -------------- | ---- |
| IMG_001  | 1              | 2.5  |
| IMG_002  | 2              | 6.8  |
| IMG_003  | 3              | -1.2 |

---

## Installation

```bash
pip install timm
pip install albumentations
pip install grad-cam
pip install openpyxl
pip install seaborn
pip install scikit-learn
pip install torch-lr-finder
```

or

```bash
pip install -r requirements.txt
```

---

## Training

Update dataset paths:

```python
BASE_DIR = "/path/to/dataset"

TRAIN_IMG_DIR = f"{BASE_DIR}/Train_Cephalograms"
VAL_IMG_DIR   = f"{BASE_DIR}/Validation_Cephalograms"
TEST_IMG_DIR  = f"{BASE_DIR}/Test_Cephalograms"

ANGLE_DIR     = f"{BASE_DIR}/angles"
```

Then run:

```bash
jupyter notebook cephalogram_classifier.ipynb
```

or

```bash
python train.py
```

---

## Inference

Load the trained model:

```python
checkpoint = torch.load("best_model.pth")
model.load_state_dict(checkpoint["model_state_dict"])
```

Inference returns:

```python
pred_class
pred_anb
```

where:

* pred_class = Skeletal Class I / II / III
* pred_anb = Predicted ANB angle

---

## Outputs

The framework automatically generates:

* Best model checkpoint
* SWA model checkpoint
* Training curves
* Classification report
* Confusion matrix
* ANB regression metrics
* Confidence intervals

All outputs are saved under:

```text
optimized_outputs/
```


## Contact

Dooman Arefan, PhD

Department of Radiology
University of Pittsburgh

Email: d.arefan139@yahoo.com
