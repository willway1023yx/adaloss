# AdaLoss: A computationally-efficient and provably convergent adaptive gradient method

This repository is the official implementation of [AdaLoss: A computationally-efficient and provably convergent adaptive gradient method](https://arxiv.org/pdf/2109.08282.pdf) (AAAI 2022).

We propose a computationally-friendly adaptive learning rate schedule, "AdaLoss", which directly uses the information of the loss function to adjust the stepsize in gradient descent methods. We prove that this schedule enjoys linear convergence in  linear regression.
Moreover, we provide a linear convergence guarantee over the non-convex regime, in the context of two-layer over-parameterized neural networks. If the width of the first-hidden layer in the two-layer networks is sufficiently large (polynomially), then AdaLoss converges robustly *to the global minimum* in polynomial time. We numerically verify the theoretical results and extend the scope of the numerical experiments by considering applications in LSTM models for text clarification and policy gradients for control problems.

## AdaLoss Algorithm
<img src="figures/adaloss.png" width=500>
In over-parameterized settings, we let c=0, alpha=1.

## Requirements
- The implementation is based on Python 3
- To install requirements:

```setup
pip install -r requirements.txt
```

## Data and Pre-trianed Models
When running the following training scripts, the code will download CIFAR100 datasets and pretrianed models automatically. We also list the source of data and pretrained models below. 
### Source of Data:
- [CIFAR100](https://www.cs.toronto.edu/~kriz/cifar.html)
### Source of Pre-trained Models: 
The scripts load pre-trianed models from the awesome pytorch image models (timm).
- [PyTorch Image Models (timm)](https://github.com/rwightman/pytorch-image-models).

## Training (Fine-tuning)
- Code for AdaLoss is implemented in ```loss.py```
- Code for training is in ```train.py``` with the following options:
```
usage: python.py [-c] [--model pretrained model] [--eta eta] [--epochs training epochs] [--weight_decay weight_decay] [--b0 initial learning rate parameter] [--adanormb0 b0 for adagrad_norm] [--alpha alpha in AdaLoss] [--cc c in AdaLoss] [--momentum optimizer momentum] [--bs training batch_size] [--seed random seed] [--name experiment name]
```
- Options ```--b0, --alpha``` and ```--cc``` are b_0, \alpha c in AdaLoss' algorithm with c=0 and alpha=1 as defaults in the over-parameterized settings.
- To fine-tuning the pre-trianed deep neural network model---ViT S/16 (vision transformer)---on CIFAR100 as the experiments in the paper, run this command:

```train
python train.py -c ./configs/config_cifar100_adaloss.json --model vits16r224 --b0 1 --eta 0.1 --epochs 10 --weight_decay 1e-4 
python train.py -c ./configs/config_cifar100_sgd_b0.json --model vits16r224 --b0 1 --eta 0.1 --epochs 10 --weight_decay 1e-4
python train.py -c ./configs/config_cifar100_sgd_sqrt.json --model vits16r224 --b0 1 --eta 0.1 --epochs 10 --weight_decay 1e-4
python train.py -c ./configs/config_cifar100_adagrad_norm.json --model vits16r224 --b0 1 --eta 0.1 --epochs 10 --weight_decay 1e-4   
```
- To fine-tuning the ResNet50-swsl model, parse ```--model swsl_resnet50```
- In the output logs, effective lr = eta/b_t at the last iteration of an epoch.

## Results

Comparison of AdaLoss (ours), AdaGrad-Norm, SGD_Constant, SGD_DecaySqrt on test accuracy on CIFAR100 by fine-tuning on pretrained DNNs, vision transformer ViT-S/16 and CNN ResNet50-swsl, with image size 224x224 and batch size 64.
Training: 45k, validation: 5k, and test: 10k. The results are mean and std over 3 runs.

### [Image Classification on CIFAR100](https://www.cs.toronto.edu/~kriz/cifar.html)
- With pretrained vision transformer, ViT-S/16

| b_0   | AdaLoss        | AdaGrad-Norm   | SGD\_Constant  | SGD\_DecaySqrt |
|-------|----------------|----------------|----------------|----------------|
| 0.01  | 90.65±0.09 | 68.34±2.87 | N/A            | 90.76±0.04 |
| 0.1   | 90.64±0.15 | 86.27±0.43 | N/A            | 90.50±0.16 |
| 1     | 90.58±0.12 | 89.33±0.02 | 83.21±0.81 | 90.52±0.07 |
| 10    | 90.50±0.17 | 90.83±0.17 | 90.36±0.11 | 90.44±0.19 |
| 100   | 89.62±0.12 | 89.99±0.11 | 89.85±0.14 | 89.58±0.06 |

- With pretrained CNN, ResNet50-swsl

| b_0   | AdaLoss    | AdaGrad-Norm | SGD\_Constant | SGD\_DecaySqrt |
|-------|------------|--------------|---------------|----------------|
| 0.01  | 84.0±0.51  | 64.57±1.70   | 2.33±0.35     | 83.66±0.13 |
| 0.1   | 84.29±0.27 | 68.24±2.55   | 26.02±6.45    | 83.70±0.38 |
| 1     | 83.96±0.64 | 80.13±1.06   | 63.36±6.69    | 83.98±0.13 |
| 10    | 84.26±0.44 | 83.02±0.79   | 81.02±0.94    | 84.79±0.20 |
| 100   | 83.03±0.19 | 83.32±0.20   | 83.53±0.13    | 83.10±0.29 |


## License 
- This repo was adapted and modified from [ELR](https://github.com/shengliu66/ELR).
- This README is formatted based on [releasing-research-code](https://github.com/paperswithcode/releasing-research-code).

## Bibtex
```@article{wu2021adaloss,
title={AdaLoss: A computationally-efficient and provably convergent adaptive gradient method},
author={Wu, Xiaoxia and Xie, Yuege and Du, Simon and Ward, Rachel},
journal={arXiv preprint arXiv:2109.08282},
year={2021}
}
```
