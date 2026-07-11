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
| **Precision** | **81.89%** | 75.00% |
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

## 📅 Week 4 Progress: Explainable AI (XAI) Pipeline Integration

### 1. Objective & Scope
Week 4 focused on transitioning our Iris Presentation Attack Detection (PAD) framework from an opaque "black-box" model into a transparent, interpretable vision pipeline. By integrating post-hoc feature attribution frameworks, we visually diagnosed the network's spatial decision boundaries and audited activation maps. This step allows us to isolate the exact regional anomalies causing the static spatial performance drop and majority-class bias observed during Week 3 noise stress testing.

### 2. Technical Implementations
* **Macroscopic Feature Routing (Grad-CAM):** Developed a custom, pure-PyTorch hook engine (`VisualGradCAMEngine`) that registers to the final convolutional block of our selected MobileNetV2 backbone (`features[18][0]`). This intercepts forward feature maps and backward target gradients to compute linearly weighted activation fields.
* **Microscopic Game-Theoretic Attributions (SHAP):** Configured game-theoretic Gradient SHAP models via the `captum` library. By evaluating expectations over lines connecting an absolute zero background baseline to the input tensor across local samples, it extracts exact, pixel-by-pixel credit/blame distributions.
* **Unified Diagnostic Visual Studio:** Synthesized raw activation matrices into high-contrast spatial layouts. Coarse attention grids are upsampled to $224 \times 224$, mapped via an OpenCV `COLORMAP_JET` thermal profile, and alpha-blended ($65\%$ image / $35\%$ heatmap) alongside pixel-level `inferno` SHAP matrices.
* **Rigorous 3-Way Data Split Validation:** Maintained complete data integrity by validating model weights against the isolated 15% Validation split before running final benchmark evaluations.

### 3. Interpretability & Failure Diagnostic Analysis
* **Visual Verification of Explainability Deliverables:** 1. **Explainable Prediction Pipeline:** Converted a blind prediction score into an auditable decision path. Every logit outcome is paired with a corresponding explainability context.
  2. **Heatmap Visualization Outputs:** Warm thermal zones (red/orange) confirm that the model focuses its macroscopic attention on critical biometric boundaries—such as papillary contours, fake lens borders, and high-frequency print artifacts—while completely ignoring background noise.
  3. **Feature Attribution Analysis:** The high-contrast pixel scatter plots reveal exactly how credit is distributed at a microscopic level, highlighting the specific micro-textures that triggered spoof detection.

### 4. Strategic Project Alignment
* **Validation Studio Layout Matrix:** When evaluating samples across the test split, the studio outputs a 4-column diagnostic plot directly below the running code:
  ```text
  [ Raw Input Iris ] ──> [ Grad-CAM Heatmap ] ──> [ Decision Overlay ] ──> [ SHAP Attribution ]

---

## 📅 Week 5 Progress: Temporal Biometrics Module

### 1. Objectives & Tasks
* **Analyze Temporal Eye Behavior:** Transition from static 2D image profiling to tracking dynamic eye behavior over consecutive frames.
* **Capture Motion-Based Liveness:** Track changes over time to differentiate living eye movement from rigid presentation attacks (printouts/replays).
* **Implement Biometric Tracking:**
  * **Blink Detection:** Monitor frame-to-frame pixel change energy spikes.
  * **Pupil Dynamics:** Model temporal fluctuations (dilation/constriction) of the pupil area.
  * **Frame Consistency:** Use structural cross-correlation coefficients to verify smooth, biological movement.

### 2. Model Architecture
A hybrid deep learning backbone optimized for time-series sequences:
* **Spatial Feature Extractor:** A frozen pre-trained **MobileNetV2** backbone that converts sequence frames into lightweight 1280-dimensional feature tensors.
* **Temporal Encoder:** A recurrent **LSTM Network** (hidden_dim=64) that processes the chronological timeline of features to calculate liveness probabilities.

### 3. Deliverables & Results
* **Temporal Biometrics Module:** Fully operational 3-way dataset split (70% Train, 15% Val, 15% Test) running high-throughput PyTorch DataLoaders.
* **Blink & Pupil Analysis System:** Real-time extraction of time-series biometric vectors.
* **Visual Diagnostic Studio:** Renders a clean 3-panel dashboard mapping **Absolute Energy Delta**, **Estimated Pupil Area**, and **Structural Correlation Scores** to provide clear project verification.

---

## 📅 Week 6 Progress: Spoof Confidence Decomposition Module

### 1. Dataset Acquisition & Environment Setup
* The script initializes execution on a CUDA GPU if one is available on the system.
* It configures Kaggle API credentials to download the **casia-iris-thousand** dataset.
* The code automatically provisions a structured directory system for isolating live samples from various spoof types (physical printed, replay, textured contact lenses, and synthetic attacks).
* Five sample image paths are loaded into memory for confidence decomposition testing.

### 2. Confidence Decomposition Engine
* A Python class named `ConfidenceDecompositionEngine` is established to separate a single liveness prediction score into three standalone factors: Texture, Motion, and Reflection.
* The class simulates the model's metric generation across five distinct presentation types.
* Live samples are algorithmically assigned high liveness probabilities, while spoofed samples receive a low overall confidence score paired with specific subsystem anomalies.

### 3. Interpretable Spoof Reasoning
* The `generate_reasoning_output` function translates numerical metrics into explicit, hardware-defensive telemetry logs.

| Biometric Sub-metric | Authentic Indicator (> 50%) | Anomaly Indicator (< 50%) |
| **Texture Confidence** | Authentic high-frequency iris patterns detected. | Artifacts, blur, print dot-matrix, or synthetic generation artifact detected. |
| **Motion Confidence** | Natural blink and pupil dynamics verified. | Static or rigid presentation detected. |
| **Reflection Confidence** | Genuine corneal specular reflection verified. | Matte surface, screen glare, or synthetic illumination profile detected. |

### 4. Visualization & Output
* The `plot_confidence_contribution` function utilizes **matplotlib** to generate a 5-panel bar graph.
* The visualization maps the separated feature contributions (Texture, Motion, and Reflection) across the Live, Print, Screen, Lens, and Synthetic scenarios.
* The final layout is saved locally as a high-resolution PNG image for project verification.

---

## 📅 Week 7 Progress: Experimental Evaluation Module

### 1. Objectives & Tasks
* **Robustness Evaluation Engine:** Built an automated testing harness to evaluate model accuracy under high-stress operational parameters.
* **Environmental Variance Controls:** Implemented adaptive lighting modifiers to evaluate recognition stability under underexposed (Low Light) and overexposed (Bright Light) environments.
* **Biometric Auditing Infrastructure:** Engineered full support for calculating standardized ISO/IEC 30107-3 error metrics, including **APCER** (Attack Presentation Classification Error Rate), **BPCER** (Bona Fide Presentation Classification Error Rate), and **ACER** (Average Classification Error Rate).
* **Diagnostic Visualizations:** Automated generation of high-resolution Confusion Matrices and Receiver Operating Characteristic (ROC) profiles.

### 2. Automated Dataset Ingestion & Isolation
* The script checks for GPU availability (`cuda`) to run hardware-accelerated batch inference.
* Configures secure Kaggle API hooks to ingest the **CASIA-Iris-Thousand** dataset (Bona Fide live samples) and the **CASIA-Iris-Syn** dataset (Synthetic samples).
* Maps local paths dynamically, isolating source assets from synthesized presentation attack vectors.

### 3. High-Fidelity Presentation Attack Simulator
* **Print Attack:** Applies structured Gaussian blurs combined with precise Look-Up Table (LUT) gamma curves to strip original high-frequency details.
* **Replay Attack:** Introduces sinusoidal mesh arrays to emulate digital monitor frequency interference and specular screen spots.
* **Contact Lens Attack:** Distributes structural micro-circles matching common cosmetic lens patterns over the real iris frame.
* **Synthetic Attack:** Multiplies specific coordinate coefficients inside the Fast Fourier Transform (FFT) frequency spectrum of pure synthetic samples.

### 4. Stress-Testing Data Pipeline
* A custom PyTorch `RobustnessEvaluationDataset` maps the compiled datasets into evaluation batches.
* Dynamically shifts image matrix values to evaluate extreme brightness thresholds (multiplied by `0.55` for severe underexposure and `1.45` for extreme glare).

### 5. Biometric Auditing & Metrics Calculation
* Passes batches through a ResNet architecture evaluation pipeline to generate test confidence rankings.
* Calculates explicit security metrics, allowing immediate diagnosis of structural weak points (e.g., verifying if textured lenses lower accuracy more than standard print vectors).

### 6. Biometric Performance Metrics Summary
The framework computes precise error parameters to ensure standard biometric security validation:

| Biometric Metric | Security Context | Operational Definition |
| **APCER** | False Acceptance Rate (FAR) | Percentage of presentation attacks incorrectly classified as live samples. |
| **BPCER** | False Rejection Rate (FRR) | Percentage of genuine (bona fide) live samples incorrectly flagged as attacks. |
| **ACER** | Overall System Balance | The average error rate balancing security (APCER) and user convenience (BPCER). |

### 7. Performance Visualizations & Logs
Upon executing the evaluation pipeline, the module saves automated analytical plots directly to the `outputs/metrics/` folder:
* **Standard Confusion Matrix:** Maps exact counts of True Positives, True Negatives, False Positives (APCER events), and False Negatives (BPCER events).
* **Receiver Operating Characteristic (ROC) Profile:** Measures the Area Under the Curve (AUC) across all evaluated stress variables to define structural liveness boundaries.

---
