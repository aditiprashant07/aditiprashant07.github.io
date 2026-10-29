---
layout: page
permalink: /projects/lora-busi/
---

# LoRA and BUSI: Breast Ultrasound Image Classification with DeiT

## 1. Project overview

This project explores parameter-efficient fine-tuning of a pretrained vision transformer for classifying breast ultrasound images from the BUSI (Breast Ultrasound Images) dataset. It uses **DeiT-Small (Data-efficient Image Transformer)** as the image-classification backbone and **Low-Rank Adaptation (LoRA)** to adapt selected attention layers to the target dataset without updating the full pretrained model.

The notebook builds a workflow covering dataset retrieval, image preprocessing, model configuration, LoRA fine-tuning, validation, classification metrics, and Grad-CAM visualizations. The task is a machine-learning experiment for medical-image classification, not a clinically validated diagnostic system.

**Repository:** [BUSI-and-LoRA-Project-](https://github.com/aditiprashant07/BUSI-and-LoRA-Project-)  
**Notebook:** [LoRAandBUSI.ipynb](https://github.com/aditiprashant07/BUSI-and-LoRA-Project-/blob/main/LoRAandBUSI.ipynb)

## 2. Objectives

- Build an image-classification workflow for the BUSI breast ultrasound dataset, with three classes: benign, malignant, and normal.
- Adapt a pretrained DeiT-Small model to the dataset using LoRA rather than fine-tuning every backbone parameter.
- Reduce the number of trainable parameters and the associated fine-tuning compute compared with updating the full model.
- Evaluate classification behaviour with validation accuracy, a classification report, and confusion-matrix-based analysis.
- Explore model interpretability with Grad-CAM heatmaps that highlight image regions influencing a prediction.

## 3. Were the objectives achieved?

**At the implementation level, the notebook lays out the main training and evaluation workflow. The final predictive performance cannot be confirmed from the committed notebook alone.**

The code includes dataset loading and preprocessing, an 80/20 train-validation split, a pretrained DeiT-Small classifier configured for three labels, LoRA adapters on the query and value attention projections, a training loop, validation-accuracy calculation, classification-report generation, and Grad-CAM setup.

However, the notebook currently has no saved cell outputs or recorded metrics. That means a final accuracy, class-wise precision/recall, or improvement over full fine-tuning cannot responsibly be claimed from the repository as it stands. The next step to establish empirical success is to execute the notebook, save the results, and ideally compare LoRA against a clearly defined baseline using the same data split and evaluation procedure.

## 4. Concepts learned and applied

- **Vision Transformers (ViT) and DeiT:** using patch-based transformer architectures for image classification and adapting a pretrained image model to a new task.
- **Transfer learning:** reusing pretrained representations instead of training an image model from scratch.
- **Low-Rank Adaptation (LoRA):** adding trainable low-rank matrices to selected model layers while freezing the base weights.
- **Parameter-efficient fine-tuning (PEFT):** restricting optimization to adapter parameters and the task-specific classification head.
- **Medical-image preprocessing:** resizing images to 224 × 224, converting them to tensors, and applying ImageNet normalization expected by the pretrained model.
- **Dataset organization and splitting:** loading folder-labelled images and creating training and validation subsets.
- **Training fundamentals:** cross-entropy loss, AdamW optimization, gradient calculation, batching, epochs, and mixed-precision training.
- **Model evaluation:** validation accuracy and class-level precision, recall, F1-score, and confusion-matrix analysis.
- **Explainable AI:** using Grad-CAM to visualize regions associated with a model's output, while recognizing that a heatmap is not proof of clinical reasoning or correctness.
- **GPU-based experimentation:** checking CUDA availability and organizing data, checkpoints, and outputs for a Google Colab workflow.

## 5. Approach and what is different about it

### Approach

1. **Prepare the environment and data.** The notebook mounts Google Drive, creates separate folders for data, checkpoints, and outputs, and retrieves the BUSI dataset using the Kaggle API.
2. **Preprocess and split images.** Images are resized to 224 × 224 and normalized with ImageNet mean and standard-deviation values. The dataset is loaded using `ImageFolder` and split into 80% training and 20% validation subsets.
3. **Load a pretrained classifier.** The notebook loads `facebook/deit-small-patch16-224` and configures the classification head for the three target classes.
4. **Apply LoRA selectively.** The base model is frozen and LoRA is configured for the attention `query` and `value` projections, using rank 8, alpha 16, and dropout 0.1. The optimizer is restricted to trainable parameters.
5. **Train and evaluate.** The model is trained for five epochs with cross-entropy loss and AdamW. The notebook then calculates validation accuracy and produces a classification report.
6. **Inspect model explanations.** Grad-CAM is configured to produce a heatmap for a validation image, providing a visual aid for inspecting the model's attention to image regions.

### What makes this approach distinct?

The central design choice is to investigate **parameter-efficient adaptation of a pretrained vision transformer for a small, specialized medical-imaging dataset**, rather than assuming the entire backbone must be fine-tuned. Freezing the base model and training LoRA adapters is intended to reduce the trainable parameter count and make experimentation more resource-conscious.

The workflow also pairs conventional classification metrics with Grad-CAM visualizations. This combines a quantitative evaluation plan with an exploratory interpretability step. These are design choices, not evidence that LoRA outperforms full fine-tuning or that the model is clinically reliable; those claims would require reproducible results and controlled comparisons.

## 6. Technologies and tools used

| Technology | Role in the project |
|---|---|
| Python | Main programming language for the experiment |
| PyTorch | Model execution, data loading, loss, optimization, and training loop |
| Hugging Face Transformers | Loading the pretrained DeiT image-classification model and image processor |
| Hugging Face PEFT | Configuring and applying LoRA adapters |
| torchvision | Image transformations, folder-based dataset loading, and data loaders |
| BUSI dataset via Kaggle API | Source of labelled breast ultrasound images |
| scikit-learn | Classification report and evaluation metrics |
| Grad-CAM / `pytorch-grad-cam` | Visual explanations for selected model predictions |
| NumPy and Matplotlib | Array operations and plotting visualizations |
| Google Colab and Google Drive | Notebook execution environment, GPU access, and persistent file organization |
| CUDA / mixed precision | GPU acceleration and lower-precision computation where supported |

## Limitations and next steps

- Run the notebook end-to-end and save training curves, validation metrics, and Grad-CAM examples in the repository.
- Compare the LoRA model with a baseline (for example, a frozen-backbone linear head or full fine-tuning) using the same split and evaluation metrics.
- Use a reproducible, preferably stratified split with a fixed random seed; where possible, check for patient-level leakage.
- Review the data-loading and model-preprocessing choices for consistency with the pretrained model's expected input pipeline.
- Treat results as experimental. A dataset-level validation score does not establish clinical utility, generalization to other hospitals, or readiness for diagnosis.
