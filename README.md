# Explainable AI for Iris Liveness Detection

An interpretable deep learning framework designed to safeguard iris biometric authentication systems against modern presentation attacks (PAs). This project transitions from a black-box model to a transparent, explainable framework using state-of-the-art XAI techniques.

---

## 📅 Week 1 Progress: Research & Environment Setup
Our initial phase focused on literature review, threat modeling, and initializing the cloud development architecture.

### 1. Presentation Attack Detection (PAD) Framework
We studied the four main structural threat categories targeting iris biometric scanners:
* **Printed Eye Images:** High-resolution 2D printouts designed to trick standard illumination sensors.
* **Replay Attacks:** Digital playback of high-definition video loops on mobile screens.
* **Synthetic Iris Patterns:** Algorithmically generated textures designed to bypass unique feature recognition.
* **Textured Contact Lenses:** Cosmetic/patterned lenses that obscure genuine iris micro-textures.

### 2. Workstation Configuration
* **Runtime:** Google Colab Pro via a dedicated **NVIDIA T4 GPU** instance.
* **Framework Stack:** Python, PyTorch, Torchvision, OpenCV, and the Kaggle API.
* **Data Flow:** Connected server-to-server cloud endpoints to download multi-gigabit datasets instantly, ensuring a zero-footprint local storage architecture.

---

## 📅 Week 2 Progress: Baseline Model Implementation
We implemented a robust data pipeline and successfully trained our initial baseline classifier.

### 1. Dataset Partitioning & Preprocessing
Images were dynamically resized to $224 \times 224$ pixels and normalized according to ImageNet standard distributions to match our deep backbone criteria.
* **Total Indexed Dataset Size:** 33,000 images
* **Training Set Split (70%):** 23,100 images
* **Validation Set Split (15%):** 4,950 images
* **Testing Set Split (15%):** 4,950 images

### 2. Core Deep Architecture
* **Backbone Network:** Pre-trained **ResNet18** CNN.
* **Custom Modifications:** Replaced the final fully connected classification head with a 2-class binary output layer (`0: Live`, `1: Spoof`).
* **Optimization Strategy:** Adam Optimizer with a learning rate of $0.0001$ over Cross-Entropy Loss.

### 3. Baseline Evaluation Metrics (On Unseen Test Data)
Our baseline model achieved exceptional validation margins on the hidden testing split:
* **Test Accuracy:** 99.92%
* **Test Precision:** 99.84% (High capability to avoid false alarms)
* **Test Recall:** 99.95% (High capability to catch spoof variants)
* **Test F1-Score:** 99.90% (Optimal harmonic balance)

---

## 📅 Week 3 Progress: Model Improvement and Comparative Analysis

### 1. Objective & Scope
Week 3 focused on advancing the evaluation pipeline from ideal laboratory conditions to real-world operational robustness. A custom data augmentation suite was developed to stress-test the system under environmental variations, followed by a comparative performance and latency benchmark between **MobileNetV2** and **EfficientNetB0** backbones.

### 2. Technical Implementations
* **Environmental Noise Simulation:** Built a pipeline using `torchvision.transforms` and `OpenCV` to apply geometric transformations (rotations, lens warping) and photometric distortions (illumination shifts, print-noise, moiré lines) replicating real-world spoof attacks.
* **Architectural Edge Benchmarking:** Implemented and compared two lightweight, deployment-ready networks using pre-trained ImageNet weights and identical training hyper-parameters.

### 3. Quantitative Performance Matrix

| Evaluation Metric | MobileNetV2 (Selected Baseline) | EfficientNetB0 |
| :--- | :--- | :--- |
| **Classification Accuracy** | **81.25%** | 69.73% |
| **Precision** | **77.42%** | 76.51% |
| **Recall (Sensitivity)** | **58.76%** | 18.64% |
| **F1-Score** | **68.42%** | 29.86% |
| **Inference Latency** | **0.613 ms** | 0.819 ms |

### 4. Bottleneck & Defect Analysis
* **The Expected Metric Drop:** While Week 2 models surpassed 99% accuracy on pristine images, introducing aggressive structural and lighting noise challenged spatial boundaries. This drop establishes an authentic, robust baseline.
* **The Low-Recall Crisis:** Both models suffered a severe drop in Recall (58.76% and 18.64%), heavily degrading F1-scores. 
  1. This stems directly from dataset class imbalance (~20k live samples vs. ~13k spoof attacks).
  2. Under severe noise, fine iris features are obscured, causing the networks to develop an operational bias toward the majority class ("Live").
  3. **MobileNetV2** exhibited significantly higher resilience, maintaining a 40.12% higher recall and faster inference ($0.613\text{ ms}$) than EfficientNetB0.

### 5. Strategic Project Alignment
* **Direct Hook for Week 4 (Explainable AI):** The 81.25% baseline sets up a perfect diagnostic playground. Integrating **Grad-CAM** and **SHAP** next week will let us visualize exactly which noise artifacts misguide the spatial attention maps.
* **Justification for Weeks 5 & 6:** The failure of static spatial features under noise proves image analysis alone is insufficient. This explicitly justifies the necessity of the upcoming **Temporal Biometrics Module** (blink/pupil dynamics) and **Spoof Confidence Decomposition Engine** to break class bias.
---
