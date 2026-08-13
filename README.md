# RF Signal Anomaly Detection using Deep Autoencoders

## Overview

This project implements an **RF Signal Anomaly Detection system using a Deep Autoencoder**.

The model is trained only on **normal RF signals** so that it learns their underlying characteristics. During testing, signals that produce a high reconstruction error are considered anomalous.

The project uses the **RadioML 2016.10A dataset** and considers high-SNR **QPSK signals** as normal signals. Jammed RF signals are simulated by adding random noise to normal signals.

---

## Problem Statement

Radio Frequency (RF) communication signals can be affected by:

* Signal jamming
* Interference
* Abnormal noise
* Unexpected changes in signal characteristics

Such abnormalities can reduce communication quality and may indicate problems or security threats in wireless communication systems.

The objective of this project is to use a **Deep Autoencoder** to learn normal RF signal behaviour and identify abnormal signals based on reconstruction error.

---

## Dataset

The project uses the **RadioML 2016.10A** dataset.

Dataset file:

`RML2016.10a_dict.dat`

The dataset contains multiple modulation schemes and Signal-to-Noise Ratio (SNR) levels.

For this project:

* **Normal Modulation:** QPSK
* **Selected SNR values:** 10, 12, 14, 16, 18
* **Samples per SNR:** 1000
* **Total selected normal samples:** 5000
* **Original signal shape:** 2 × 128
* **Flattened input size:** 256 features

The two channels represent the **I (In-phase)** and **Q (Quadrature)** components of the RF signal.

> The RadioML dataset file is not included in this repository and should be obtained separately.

---

## Methodology

The overall workflow of the project is:

1. Load the RadioML 2016.10A dataset.
2. Select high-SNR QPSK signals as normal RF signals.
3. Flatten each `2 × 128` RF signal into a 256-dimensional feature vector.
4. Normalize the RF signal data.
5. Split the normal signals into training and testing sets.
6. Train a Deep Autoencoder only on normal RF signals.
7. Generate simulated jammed signals by adding random noise.
8. Reconstruct normal and jammed signals using the trained Autoencoder.
9. Calculate the Mean Squared Reconstruction Error.
10. Determine an anomaly detection threshold.
11. Classify signals as Normal or Anomaly.

---

## Deep Autoencoder Architecture

The Autoencoder uses fully connected Dense layers.

```text
Input
  ↓
256 Features
  ↓
Dense(64) - ReLU
  ↓
Dense(32) - ReLU
  ↓
Bottleneck(32) - ReLU
  ↓
Dense(32) - ReLU
  ↓
Dense(64) - ReLU
  ↓
Output(256) - Linear
```

The **32-dimensional bottleneck layer** learns a compressed representation of normal RF signal behaviour.

### Training Configuration

* Optimizer: Adam
* Learning Rate: 0.0005
* Loss Function: Mean Squared Error (MSE)
* Maximum Epochs: 50
* Batch Size: 32
* Early Stopping Patience: 5

---

## Simulating RF Signal Anomalies

To evaluate anomaly detection, jammed RF signals are simulated by adding Gaussian random noise to normal test signals.

The notebook uses:

```text
Jamming Noise Factor = 0.8
```

This produces signals that differ significantly from the normal RF signal patterns learned by the Autoencoder.

---

## Reconstruction Error

The Autoencoder attempts to reconstruct every input signal.

Reconstruction error is calculated using Mean Squared Error:

```text
MSE = mean((Original Signal - Reconstructed Signal)²)
```

Normal signals are similar to the data used during training and therefore generally produce **low reconstruction error**.

Jammed or abnormal signals differ from the learned normal behaviour and generally produce **high reconstruction error**.

---

## Anomaly Detection Threshold

The anomaly threshold is calculated using the:

**95th percentile of reconstruction errors from normal signals**

Classification is performed as follows:

```text
Reconstruction Error <= Threshold → Normal

Reconstruction Error > Threshold → Anomaly
```

---

## Evaluation

The notebook evaluates the anomaly detection system using:

* Reconstruction Error
* Accuracy
* Confusion Matrix
* Classification Report
* Reconstruction Error Histogram
* Normal vs Jammed Signal Visualization
* Original vs Reconstructed Signal Comparison

These visualizations help demonstrate the difference between normal and anomalous RF signals.

---

## Technologies Used

* Python
* NumPy
* Matplotlib
* Scikit-learn
* TensorFlow
* Keras
* Jupyter Notebook

---

## Project Structure

```text
RF-Signal-Anomaly-Detection/
│
├── rf_signal_anomaly_detection_autoencoder_clean.ipynb
├── README.md
└── RML2016.10a_dict.dat   # Dataset (not uploaded to GitHub)
```

---

## How to Run

### 1. Clone the repository

```bash
git clone <your-repository-url>
```

### 2. Install the required Python libraries

```bash
pip install numpy matplotlib scikit-learn tensorflow jupyter
```

### 3. Download the RadioML 2016.10A dataset

Place the following dataset file in the project directory:

```text
RML2016.10a_dict.dat
```

### 4. Start Jupyter Notebook

```bash
jupyter notebook
```

### 5. Open the notebook

Open:

```text
rf_signal_anomaly_detection_autoencoder_clean.ipynb
```

Run the cells sequentially to train and evaluate the RF anomaly detection model.

---

## Key Idea

The main idea behind the project is:

> Train the Autoencoder to understand only normal RF behaviour. If the Autoencoder cannot reconstruct a new signal accurately, the signal may be anomalous.

This allows anomaly detection without requiring the model to be trained on every possible type of abnormal RF signal.

---

## Conclusion

This project demonstrates the use of a **Deep Autoencoder for RF signal anomaly detection**.

The Autoencoder learns normal high-SNR QPSK RF signal patterns and reconstructs them with relatively low error. Simulated jammed RF signals produce higher reconstruction errors because their characteristics differ from the learned normal behaviour.

By applying a threshold based on the normal reconstruction-error distribution, the system can distinguish between normal and anomalous RF signals.

The project demonstrates how **unsupervised/deep-learning-based anomaly detection** can be applied to wireless communication and RF signal analysis.
