# Explainable AI for Iris Liveness Detection

An interpretable deep learning framework designed to safeguard iris biometric authentication systems against modern presentation attacks (PAs). This project transitions from a black-box model to a transparent, explainable framework using state-of-the-art XAI techniques.

---

## 📅 Week 1 Progress: Research & Environment Setup
Our initial phase focused on literature review, threat modeling, and initializing the cloud development architecture.

### 🧠 1. Presentation Attack Detection (PAD) Framework
We studied the four main structural threat categories targeting iris biometric scanners:
* **Printed Eye Images:** High-resolution 2D printouts designed to trick standard illumination sensors.
* **Replay Attacks:** Digital playback of high-definition video loops on mobile screens.
* **Synthetic Iris Patterns:** Algorithmically generated textures designed to bypass unique feature recognition.
* **Textured Contact Lenses:** Cosmetic/patterned lenses that obscure genuine iris micro-textures.

### 💻 2. Workstation Configuration
* **Runtime:** Google Colab Pro via a dedicated **NVIDIA T4 GPU** instance.
* **Framework Stack:** Python, PyTorch, Torchvision, OpenCV, and the Kaggle API.
* **Data Flow:** Connected server-to-server cloud endpoints to download multi-gigabit datasets instantly, ensuring a zero-footprint local storage architecture.

---

## 📅 Week 2 Progress: Baseline Model Implementation
We implemented a robust data pipeline and successfully trained our initial baseline classifier.

### 📊 1. Dataset Partitioning & Preprocessing
Images were dynamically resized to $224 \times 224$ pixels and normalized according to ImageNet standard distributions to match our deep backbone criteria.
* **Total Indexed Dataset Size:** 33,000 images
* **Training Set Split (70%):** 23,100 images
* **Validation Set Split (15%):** 4,950 images
* **Testing Set Split (15%):** 4,950 images

### 🏋️ 2. Core Deep Architecture
* **Backbone Network:** Pre-trained **ResNet18** CNN.
* **Custom Modifications:** Replaced the final fully connected classification head with a 2-class binary output layer (`0: Live`, `1: Spoof`).
* **Optimization Strategy:** Adam Optimizer with a learning rate of $0.0001$ over Cross-Entropy Loss.

### 🔬 3. Baseline Evaluation Metrics (On Unseen Test Data)
Our baseline model achieved exceptional validation margins on the hidden testing split:
* **Test Accuracy:** 99.92%
* **Test Precision:** 99.84% (High capability to avoid false alarms)
* **Test Recall:** 99.95% (High capability to catch spoof variants)
* **Test F1-Score:** 99.90% (Optimal harmonic balance)

---
