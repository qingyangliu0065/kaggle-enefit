# Enefit - Predict Energy Behavior of Prosumers

This repository contains the notebook from our final submission to the [Enefit Kaggle Competition](https://www.kaggle.com/competitions/predict-energy-behavior-of-prosumers).

## **0. background**
The number of prosumers is rapidly increasing, and solving the problems of energy imbalance and their rising costs is vital. If left unaddressed, this could lead to increased operational costs, potential grid instability, and inefficient use of energy resources. If this problem were effectively solved, it would significantly reduce the imbalance costs, improve the reliability of the grid, and make the integration of prosumers into the energy system more efficient and sustainable. Moreover, it could potentially incentivize more consumers to become prosumers, knowing that their energy behavior can be adequately managed, thus promoting renewable energy production and use.

---

## **1. Data Overview & Feature Engineering**
### **Data Source**
- Electricity production and consumption data from **Estonian prosumers with solar panels**.

### **Time Scope**
- **Training:** August 31, 2021 – May 31, 2023  
- **Testing:** June 1, 2023 – August 31, 2023  
- **1-hour granularity (~2M entries)**  

### **Feature Engineering**
- **Date-Time Features:** Extracted **hour, day, weekday, month, holiday indicators** to capture seasonal trends.  
- **Lagged Features:**  
  - **Past energy production/consumption values (2h–14 days lag)**  
  - **Rolling window statistics** (mean, std) to capture temporal dependencies.  
- **Weather & Market Features:**  
  - **Temperature, solar radiation, precipitation**  
  - **Gas & electricity prices** to account for market trends.  
- **Target Transformation:** Created a **48-hour lagged target difference** for an alternative prediction approach.  

**Feature Selection:**  
Used **Leave-One-Feature-Out (LOFO) importance ranking**, identifying **Target_168h, Weekday, and Direct Solar Radiation Forecast** as the most influential features.

---

## **2. Model Selection & Training**
The forecasting task used a combination of **tree-based models and deep learning architectures**:

| **Model**    | **Pros**                                       | **Cons**                                           |
|-------------|---------------------------------|----------------------------------|
| **LightGBM** | Efficient, robust, well-suited for tabular data | Prone to overfitting |
| **XGBoost**  | High flexibility, good for imbalanced data      | Computationally expensive |
| **DNNs**     | High flexibility, learns complex patterns      | Less interpretable, struggles with sequential dependencies |
| **LSTM**     | Expert in time-series forecasting              | Performs poorly on heterogeneous tabular data |

### **Model Training Approach**
- **Cross-validation** was performed to fine-tune hyperparameters.
- **Predictions were generated on both the original target and the 48-hour lagged target difference.**

---

## **3. Ensemble Learning for Prediction**
A **weighted ensemble model** was used to improve accuracy by combining predictions from multiple models:

1. **LightGBM predictions on the original target** (weighted **0.45**).
2. **LightGBM predictions on the target difference, then adjusted back** (weighted **0.55**).

This **ensemble strategy** helped refine forecasts and reduce model variance.

---

## Evaluation
Predictive performance are evaluated on the Mean Absolute Error (MAE) between the predicted return and the observed target.

---

## Result
Our final submission ranked in the **top 7%** (**187th out of 2,731**) among all participants.
