Transfer Learning Experiment

Objective

Investigate how progressive layer unfreezing affects the performance of an ImageNet-pretrained ResNet18 during transfer learning.

Experimental Setup

* Model: ResNet18 (ResNet18_Weights.DEFAULT)
* Optimizer: SGD
* Learning rate: η = 0.1
* Epochs: 10
* Evaluation metric: Top-1 Test Accuracy

Results

Configuration	Trainable Layers	Test Accuracy	Training Time	Evaluation Time
Pretrained baseline	None	10.54%	—	26.59 sec
Feature extraction	FC	80.98%	24.7 min	24.06 sec
Partial fine-tuning	Layer4 + FC	93.02%	27.1 min	24.02 sec
Full fine-tuning	All layers	94.10%	43.6 min	24.08 sec

Performance Analysis

The pretrained baseline achieved 10.54% accuracy without target-task training. Training only the classification head increased accuracy to 80.98%, representing a +70.44 percentage-point improvement.

Unfreezing Layer4 increased performance to 93.02%, providing a further +12.04 pp improvement over feature extraction.

Full fine-tuning achieved the highest accuracy at 94.10%, with an additional +1.08 pp improvement. However, it required substantially more training time than partial fine-tuning.

Key Findings

* Pretrained features provided a strong starting representation.
* Adapting deeper layers produced substantial performance gains.
* Layer4 + FC captured most of the improvement achieved through full fine-tuning.
* Full fine-tuning achieved the highest accuracy but required more computational time.
* Evaluation time remained approximately 24 seconds across all configurations.

Conclusion

The experiment demonstrates that progressive unfreezing can substantially improve transfer-learning performance. Full fine-tuning achieved 94.10%, while partial fine-tuning of Layer4 + FC achieved 93.02% with considerably lower training cost.
