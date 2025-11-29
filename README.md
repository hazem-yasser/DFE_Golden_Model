
# Digital Front End (DFE) – Golden Model

This repository contains the Python-based **Golden Model** for a hardware Digital Front End (DFE) design.  
The project implements a multi-stage signal processing chain for sample rate conversion, interference suppression, and configurable decimation with **bit-true fixed-point simulation**.

**Author:**  
**Hazem Yasser Mahmoud Mohamed**

---

## 📂 Repository Structure
```text
DFE_GOLDEN_MODEL/
├── documentation/       # Project reports (PDFs)
├── images/              # System diagrams and plot exports
├── results/             # Quantized coefficient files & Q_15_hex
├── filterdesign.ipynb   # Filter design + coefficient quantization
└── goldenmodel.ipynb    # Main fixed-point golden model
```
---

## ⚙️ System Architecture

The DFE processes a digitized **9 MHz** 16-bit input signal using three main stages:

---

### **1️⃣ Stage 1: Polyphase Rational Resampler**

- **Function:** Resamples input from **9 MHz → 6 MHz**  
- **Structure:** Polyphase FIR using rational resampling (**L = 2, M = 3**)  
- **Optimization:** Filtering is performed at the lower rate using polyphase decomposition  
- **Coefficients:** Kaiser-window FIR, quantized to **Q1.15**

---

### **2️⃣ Stage 2: Notch Filter**

- **Function:** Suppresses interference centered at **2.4 MHz**  
- **Structure:** 2nd-order IIR biquad (Direct Form II Transposed)  
- **Quantization:** Coefficients stored as **Q2.14** for stability (values exceed 1.0)  
- **Notes:** Pole-zero placement verified to remain inside the unit circle after quantization

---

### **3️⃣ Stage 3: CIC Decimator & Compensation**

- **Function:** Provides configurable decimation and passband correction  
- **Supported Decimation Factors:** **R = 1, 2, 4, 8, 16**  
- **Subcomponents:**  
  - **CIC:** 5-stage integrator–comb  
  - **Compensation FIR:** Corrects CIC droop  
- **Output Rate:**  
  - R = 1 → 6 MHz  
  - R = 16 → 375 kHz  

---

## 🚀 How to Run

The project consists of **two Jupyter Notebooks**, executed in order:

---

### **1. `filterdesign.ipynb` — Filter Design & Coefficient Generation**

This notebook performs:

- FIR / IIR / CIC-compensation filter design  
- Stability & frequency response analysis  
- Fixed-point quantization:  
  - FIR → **Q1.15**  
  - IIR → **Q2.14**  
- Exports coefficient files to `results/`  
  - Example:  
    - `coeffs_fixed_q15.txt`  
    - `notch_a_q14.txt`

---

### **2. `goldenmodel.ipynb` — Bit-True Fixed-Point Simulation**

Implements the hardware-accurate model:

- Imports quantized coefficients  
- Simulates:  
  - Polyphase FIR loops  
  - DFII-Transposed IIR  
  - CIC filtering + compensation  
- Performs bit-shifting, truncation, saturation  
- **Verification:**  
  - Compares custom fixed-point output vs. SciPy floating-point reference  
- **Testing:**  
  - Single-tone  
  - Multi-tone  
  - Wideband noise  

---

## 📊 Results Summary

The Golden Model demonstrates:

### ✔ Stability
- IIR notch filter remains stable after Q2.14 quantization  
- Poles stay inside the unit circle

### ✔ Accuracy
- Very low **MSE** between fixed-point and floating-point outputs  
- Confirms correctness of fixed-point logic

### ✔ Spectral Performance
- **Stage 1 (Resampler):** >80 dB stopband attenuation  
- **Stage 2 (Notch):** ~60 dB attenuation at 2.4 MHz  
- **Stage 3 (CIC):** Proper aliasing suppression for **R = 2–16**

See `documentation/Golden Model report.pdf` for full plots, tables, and analysis.

---

## 📝 License

This project is for educational and research purposes.


