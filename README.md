# Reverse Engineering a Black-Box Fatigue Life Estimation Tool (Surrogate ML Model)

This project builds a **machine-learning surrogate** to replicate the behavior of a **black-box fatigue-life executable** (no source code/documentation available).  
The surrogate predicts fatigue life **N (cycles)** from 5 mechanical inputs and handles special cases where the executable returns **0 life** or **∞ life**. 

---

## Problem
We were given a fatigue-life prediction executable that maps 5 inputs → fatigue life output, but the internal logic was unknown.  
Goal: **reverse engineer** the input–output behavior and create a surrogate model with **<2% error** vs the executable. 
---

## Inputs / Output
**Inputs (5):**
- **Rm**: tensile strength  
- **Kt**: stress concentration factor  
- **G**: stress gradient  
- **Rz**: surface roughness  
- **σa**: nominal stress amplitude 

**Output:**
- fatigue life **N (cycles)** with three possible regimes:
  - **0** (immediate failure)
  - **finite life**
  - **∞** (infinite/safe life)
---

## Dataset Generation
We generated training data by sampling the 5D input space using **Latin Hypercube Sampling (LHS)** for good coverage, then querying the executable to obtain the corresponding fatigue life outputs. 

---

## Approach
Because outputs can be **0** or **∞**, a single regressor is not sufficient.  
We use a **two-stage hybrid pipeline**:

1) **Classifier** predicts the regime: **{0, finite, ∞}**  
2) If **finite**, a **regressor** predicts fatigue life (log-scale for stability) :contentReference[oaicite:6]{index=6}

Models evaluated include Random Forest and gradient boosting variants (XGBoost/LightGBM). The final model combines the best classifier + regressor. 

---

## Results
- Baselines: RF ~6.5% MAPE, XGBoost/LightGBM ~7.1% MAPE  
- **Final hybrid model:** **≈ 1.83% MAPE** vs the executable (meets <2% target) 

### Example plots
- Predicted vs Executable scatter plot
- S–N curves grouped by Rm and Kt

> Add screenshots here:
> <img width="1000" height="704" alt="image" src="https://github.com/user-attachments/assets/78d88a55-dbca-48d5-b59c-31f425d1f0ca" />

> <img width="1043" height="766" alt="image" src="https://github.com/user-attachments/assets/0a18471a-06e0-478f-b32a-644f745491f4" />


---

## Repository Structure
