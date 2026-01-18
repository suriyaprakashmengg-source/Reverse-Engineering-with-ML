# Reverse Engineering a Black-Box Fatigue Life Estimation Tool (Surrogate ML Model)

This project builds a **machine-learning surrogate** to replicate the behavior of a **black-box fatigue-life executable** (no source code/documentation available).  
The surrogate predicts fatigue life **N (cycles)** from 5 mechanical inputs and handles special cases where the executable returns **0 life** or **∞ life**. :contentReference[oaicite:1]{index=1}

---

## Problem
We were given a fatigue-life prediction executable that maps 5 inputs → fatigue life output, but the internal logic was unknown.  
Goal: **reverse engineer** the input–output behavior and create a surrogate model with **<2% error** vs the executable. :contentReference[oaicite:2]{index=2}

---

## Inputs / Output
**Inputs (5):**
- **Rm**: tensile strength  
- **Kt**: stress concentration factor  
- **G**: stress gradient  
- **Rz**: surface roughness  
- **σa**: nominal stress amplitude :contentReference[oaicite:3]{index=3}

**Output:**
- fatigue life **N (cycles)** with three possible regimes:
  - **0** (immediate failure)
  - **finite life**
  - **∞** (infinite/safe life) :contentReference[oaicite:4]{index=4}

---

## Dataset Generation
We generated training data by sampling the 5D input space using **Latin Hypercube Sampling (LHS)** for good coverage, then querying the executable to obtain the corresponding fatigue life outputs. :contentReference[oaicite:5]{index=5}

---

## Approach
Because outputs can be **0** or **∞**, a single regressor is not sufficient.  
We use a **two-stage hybrid pipeline**:

1) **Classifier** predicts the regime: **{0, finite, ∞}**  
2) If **finite**, a **regressor** predicts fatigue life (log-scale for stability) :contentReference[oaicite:6]{index=6}

Models evaluated include Random Forest and gradient boosting variants (XGBoost/LightGBM). The final model combines the best classifier + regressor. :contentReference[oaicite:7]{index=7}

---

## Results
- Baselines: RF ~6.5% MAPE, XGBoost/LightGBM ~7.1% MAPE  
- **Final hybrid model:** **≈ 1.83% MAPE** vs the executable (meets <2% target) :contentReference[oaicite:8]{index=8}

### Example plots
- Predicted vs Executable scatter plot
- S–N curves grouped by Rm and Kt

> Add screenshots here:
> ![Predicted vs Executable](figures/pred_vs_exe.png)
> ![S-N Curves](figures/sn_curves.png)

---

## Repository Structure
