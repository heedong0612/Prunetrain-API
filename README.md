[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://bitbucket.org/lph_tools/prunetrain/src/4ff58b6bf968fb4b6aedef17fca3fa6916d22078/LICENSE.md?at=master&fileviewer=file-view-default)

# Prerequisites
1. An Azure subscription. 
2. A terminal and Python >= 3.6.2 and pip >=20.2.4

# Setup
1. If you do not have an Azure ML workspace, create an Azure ML workspace https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal#create-a-workspace
2. Run Jupyter Notebooks in your workspace: https://docs.microsoft.com/en-us/azure/machine-learning/how-to-run-jupyter-notebooks
3. Create GPU compute instance (NVIDIA Tesla V100 Recommended) and click start compute
4. Clone this repository and install required packages:
```
git clone git@github.com:heedong0612/Prunetrain-API.git
cd Prunetrain-API
```
5. Running experiments using Azure CLI:
* Connect Visual Studio Code through compute instance
* Create a supporting environment on Azure 
```
az ml environment register envs
```
* Run experiement with command
```
az ml job create run_training.yml
```
* The experiement results will show up in experiments under Asset in workspace portal

# Training Examples

* Training ResNet32 on CIFAR10 with 1 GPU
```
python run-script.py --data-path /path/to/dataset --dataset cifar10 --model resnet32 --num-gpus 1
```

* Training VGG11 on CIFAR100 with 2 GPU
```
python run-script.py --data-path /path/to/dataset --dataset cifar100 --model vgg11 --num-gpus 2
```

* Training ResNet50 on ImageNet with 4 GPU
```
python run-script.py --data-path /path/to/dataset --dataset imagenet --model resnet50 --num-gpus 4
```

* Training ResNet50 on ImageNet with 4 GPU and regularization penalty ratio of 0.3
```
python run-script.py --data-path /path/to/dataset --dataset imagenet --model resnet50 --num-gpus 4 --penalty-ratio 0.3
```

# About PruneTrain

This repository contains the source codes and scripts for training a set of CNNs using PruneTrian, continuous structured model pruning and network architecture reconfiguration for faster neural network training (https://arxiv.org/pdf/1901.09290.pdf). We provide the code base to train ResNet and VGG models with different number of layers for CIFAR10, CIFAR100, and ResNet50 model for ImageNet.

We flatten CNN architectures (each layer module definition) to support easy network architecture reconfiguration and generation into python files, where we use metadata programming. After pruning the CNN model using group lasso regularization, each layer has different channel dimensions. To store layers each with different channel dimensions, it is rather convenient to flatten the network layer structure than building with nested loops. It is possible to modify the module status without generating as a file. However, we do this for simpler pruning history track with intermediate checkpoints (model file and network file).

# Training Results

We train CNN models on both CIFAR10/100 for 182 epochs and ResNet50 on ImageNet for 90 epochs. We provide the pruned models and the reconfigured network architecture files (auto generated by our framework).

| Dataset        | Model           | Removed training FLOPs | Removed inference FLOPs  | Top1 error (fine-tuning) | Model | Network |
|----------------|:---------------:|:----------------------:|:------------------------:|:------------:|:-----:|:--------------------:|
| CIFAR10        | ResNet32        | 53%                    |   66%                    | 91.8%        | | [Link](https://bitbucket.org/lph_tools/prunetrain/downloads/arch_cifar10_resnet32.py)|
| CIFAR10        | ResNet50        | 50%                    |   70%                    | 93.1%        | | [Link](https://bitbucket.org/lph_tools/prunetrain/downloads/arch_cifar10_resnet50.py)|
| CIFAR100       | ResNet32        | 32%                    |   46%                    | 69.5%        | | [Link](https://bitbucket.org/lph_tools/prunetrain/downloads/arch_cifar100_resnet32.py)|
| CIFAR100       | ResNet50        | 53%                    |   69%                    | 72.4%        | | [Link](https://bitbucket.org/lph_tools/prunetrain/downloads/arch_cifar100_resnet50.py)|
| ImageNet       | ResNet50        | 37%                    |   53%                    | 74.4% (74.6%)|       |                      |
| ImageNet       | ResNet50        | 29%                    |   43%                    | 74.7% (75.2%)|       |                      |

