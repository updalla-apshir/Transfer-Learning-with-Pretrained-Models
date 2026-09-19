# Transfer Learning Experiment 

## Objective

Investigate how progressive layer unfreezing affects the performance and computational cost of an ImageNet-pretrained ResNet18 during transfer learning.

The experiment compares four configurations, ranging from using the pretrained model without training to full fine-tuning of all model parameters.

--- 

## Experimental Setup

| Parameter          | Configuration                 |
| ------------------ | ----------------------------- |
| Model              | ResNet18                      |
| Pretrained Weights | `ResNet18_Weights.DEFAULT`    |
| Optimizer          | SGD                           |
| Learning Rate      | 0.1                           |
| Epochs             | 10                            |
| Evaluation Metric  | Top-1 Test Accuracy           |
| Input              | Resized to 224×224, 3-channel |
| Initialization     | ImageNet-pretrained weights   |

### Transfer Learning Configurations

1. **Pretrained baseline** — no trainable layers
2. **Feature extraction** — classification head (`FC`) trainable
3. **Partial fine-tuning** — `Layer4 + FC` trainable
4. **Full fine-tuning** — all layers trainable

---

## Results

| Configuration       | Trainable Layers | Test Accuracy | Training Time | Evaluation Time |
| ------------------- | ---------------- | ------------: | ------------: | --------------: |
| Pretrained baseline | None             |        10.54% |             — |       26.59 sec |
| Feature extraction  | FC               |        80.98% |      24.7 min |       24.06 sec |
| Partial fine-tuning | Layer4 + FC      |        93.02% |      27.1 min |       24.02 sec |
| Full fine-tuning    | All layers       |    **94.10%** |      43.6 min |       24.08 sec |

---

## Performance Analysis

### 1. Pretrained Baseline

The pretrained ResNet18 achieved **10.54% test accuracy** without target-task training.

This is close to chance-level performance for the 10-class FashionMNIST classification task because the original classification head was trained for ImageNet's 1,000 classes rather than FashionMNIST's 10 classes.

---

### 2. Feature Extraction — FC Only

Training only the final classification layer increased test accuracy to **80.98%**.

This represents a:

**+70.44 percentage-point improvement**

over the pretrained baseline.

The result demonstrates that the pretrained ImageNet representation already provides useful visual features that can be adapted to the target dataset by training only a new classification head.

---

### 3. Partial Fine-Tuning — Layer4 + FC

Unfreezing `Layer4` in addition to the classification head increased test accuracy to **93.02%**.

Compared with feature extraction:

**93.02% − 80.98% = +12.04 percentage points**

Training time increased only slightly:

* FC: **24.7 min**
* Layer4 + FC: **27.1 min**

This indicates that adapting the deeper feature representations provided a substantial performance improvement without requiring the computational cost of full fine-tuning.

---

### 4. Full Fine-Tuning

Allowing all ResNet18 layers to update produced the highest test accuracy:

**94.10%**

This is an additional:

**+1.08 percentage points**

over the Layer4 + FC configuration.

However, training time increased considerably:

* Layer4 + FC: **27.1 min**
* Full fine-tuning: **43.6 min**

This represents approximately **61% more training time** for only a **1.08 percentage-point** increase in test accuracy.

---

## Evaluation Time

Evaluation time remained approximately constant across the trained configurations:

* Feature extraction: **24.06 sec**
* Layer4 + FC: **24.02 sec**
* Full fine-tuning: **24.08 sec**

The small differences indicate that the number of trainable parameters primarily affected **training time**, rather than evaluation time.

The pretrained baseline took **26.59 seconds**, which was also close to the evaluation times of the trained models.

---

## Key Findings

* ImageNet-pretrained features provided a strong starting representation for FashionMNIST.
* Training only the classification head increased accuracy from **10.54% to 80.98%**.
* Unfreezing `Layer4` produced a substantial additional improvement to **93.02%**.
* Full fine-tuning achieved the highest accuracy at **94.10%**.
* The improvement from `Layer4 + FC` to full fine-tuning was relatively small (**+1.08 pp**).
* Full fine-tuning required substantially more training time (**43.6 min vs. 27.1 min**).
* Evaluation time remained approximately constant at **~24 seconds** across trained configurations.
* The experiment demonstrates **diminishing returns from progressive unfreezing**.

---

## Accuracy Improvement

The progressive improvement can be summarized as:

```text
Pretrained baseline
10.54%
   │
   │ +70.44 pp
   ▼
FC only
80.98%
   │
   │ +12.04 pp
   ▼
Layer4 + FC
93.02%
   │
   │ +1.08 pp
   ▼
Full fine-tuning
94.10%
```

---

## Accuracy vs. Training Cost

The results show an important trade-off between accuracy and computational cost.

`Layer4 + FC` achieved **93.02% accuracy in 27.1 minutes**, while full fine-tuning achieved **94.10% in 43.6 minutes**.

Therefore, in this particular experiment, most of the performance improvement from fine-tuning was obtained by adapting `Layer4` and the classification head. Further unfreezing of the remaining layers produced a much smaller accuracy improvement relative to the additional training time.

This should be interpreted as an observation from this experiment rather than a general rule for all datasets and transfer-learning tasks.

---

## Conclusion

This experiment demonstrates that progressive unfreezing can substantially improve transfer-learning performance.

Starting from an ImageNet-pretrained ResNet18, training only the classification head increased test accuracy from **10.54% to 80.98%**. Unfreezing `Layer4` further increased accuracy to **93.02%**, showing that adapting deeper pretrained features was highly beneficial.

Full fine-tuning achieved the highest accuracy at **94.10%**, but required **43.6 minutes** of training compared with **27.1 minutes** for `Layer4 + FC`.

The results therefore demonstrate **diminishing returns as more layers are unfrozen**: `Layer4 + FC` captured most of the achievable improvement in this experiment, while full fine-tuning provided only an additional **1.08 percentage points** at substantially higher training cost.

Evaluation time remained approximately constant across configurations at around **24 seconds**, showing that the major computational difference between the configurations occurred during training rather than evaluation.

> **Main result:** Progressive unfreezing substantially improved transfer-learning performance, while the `Layer4 + FC` configuration achieved most of the benefit of full fine-tuning at considerably lower training cost.
