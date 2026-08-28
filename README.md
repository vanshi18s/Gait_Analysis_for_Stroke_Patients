# Post-Stroke Gait Analysis Based on Kinematics Data and Interpretability Using Explainable AI Techniques

**Author:** Vanshi Sethi
**Institution:** Indian Institute of Technology Guwahati, India (B.Sc. Honours Data Science and Artificial Intelligence) / NIT Silchar (Internship) 
**Contact:** s.vanshi@op.iitg.ac.in

---

## 📌 Abstract / Overview
Stroke frequently leaves survivors with persistent gait abnormalities that impair mobility and reduce their quality of life. Objective, data-driven gait assessment is valuable for diagnosis, treatment planning, and monitoring recovery. This project investigates whether kinematics data alone can retain comparable diagnostic value to multimodal setups that require electromyography (EMG) acquisition. 

Using lower-limb joint angle data (ankle, knee, hip, pelvis) from 138 healthy controls (HC) and 50 post-stroke (SP) individuals, entropy-based complexity measures were computed to train seven machine learning classifiers. Furthermore, Explainable AI (XAI) techniques (SHAP and LIME) were applied to ensure clinical interpretability by identifying which anatomical mechanics drive the predictions.

## 🎯 Problem Statement and Objectives
While machine learning models can classify post-stroke versus healthy gait with high accuracy, most act as black boxes. This poses a barrier to clinical trust, as these models reveal nothing about which biomechanical features drive a prediction. This project addresses building an accurate and explainable pipeline.

**Key Objectives:**
*   Clean and preprocess raw kinematic gait-cycle data, ensuring per-subject identity preservation and quality checks.
*   Perform an entropy-based feature extraction utilizing multiscale entropy (ApEn, SampEn, FuzzyEn, SpEn, CondEn).
*   Apply MRMR feature selection and nested cross-validation across seven classifiers.
*   Evaluate classifiers using accuracy, ROC-AUC, precision, and recall.
*   Apply SHAP and LIME to identify which gait features most influence SP-vs-HC predictions.

## ⚙️ Methodology

### 1. Data Acquisition & Cleaning
*   Joint angle trajectories were extracted from the publicly available Van Criekinge et al. dataset.
*   The data utilized the right side for healthy controls and the paretic side for stroke patients.
*   Raw signals from the Ankle, Knee, Hip, and Pelvis were converted to one-dimensional arrays with missing samples removed.

### 2. Feature Extraction
Five complexity metrics were computed independently for each joint's angular trajectory over the gait cycle:
*   **Approximate Entropy (ApEn):** Measures the predictability or regularity of a signal.
*   **Sample Entropy (SampEn):** A more robust version of ApEn that is less sensitive to data length and removes "self-match" bias.
*   **Fuzzy Entropy (FuzzyEn):** Uses a gentle gradient (exponential membership function) to assign similarity scores, making the metric highly resistant to noise.
*   **Spectral Entropy (SpEn):** Evaluates the complexity of the signal's frequency distribution. The first four statistical moments (Mean, Standard Deviation, Skewness, and Kurtosis) of the distribution were computed to capture the exact distributional shape.

### 3. Model Training & Validation
*   The majority class (HC) was randomly downsampled to match SP on each iteration (50 vs. 50).
*   MRMR feature selection was applied inside a 10-fold nested stratified cross-validation loop, selecting the top 10 features per fold to avoid data leakage.
*   Hyperparameters were tuned via randomized search across seven classifiers: Coarse Tree, Logistic Regression, Kernel Naive Bayes, Cubic SVM, Fine KNN, Bagged Trees, and Medium Neural Network.

## 📊 Results & Performance
Non-linear margin-based and ensemble architectures demonstrated superior classification power. 

*   **Top Accuracy:** Cubic SVM, Logistic Regression, and Medium NN achieved the best performance with **92.3% accuracy**.
*   **Best Class Separability:** Bagged Trees achieved the strongest AUC ($0.97 \pm 0.05$).

*Performance Summary Table:*

| Model | Accuracy (%) | Precision (%) | Recall (%) | AUC |
| :--- | :--- | :--- | :--- | :--- |
| **Coarse Tree** | 88.2 ± 10.7 | 92.2 ± 11.4 | 84.9 ± 17.6 | 0.90 ± 0.10 |
| **Logistic Reg.** | 92.3 ± 8.7 | 97.3 ± 7.3 | 87.6 ± 15.5 | 0.95 ± 0.07 |
| **Naive Bayes** | 91.5 ± 8.7 | 97.2 ± 6.9 | 85.9 ± 15.4 | 0.93 ± 0.08 |
| **Cubic SVM** | 92.3 ± 8.2 | 97.1 ± 7.1 | 87.7 ± 14.6 | 0.95 ± 0.06 |
| **Fine KNN** | 90.0 ± 8.7 | 98.4 ± 5.8 | 81.5 ± 16.2 | 0.92 ± 0.08 |
| **Bagged Trees** | 91.6 ± 8.2 | 95.8 ± 8.7 | 88.0 ± 13.2 | 0.97 ± 0.05 |
| **Medium NN** | 92.3 ± 8.7 | 96.7 ± 7.7 | 88.0 ± 14.7 | 0.95 ± 0.07 |

## 🧠 Explainable AI (XAI) Insights
Post-hoc SHAP and LIME were utilized to extract global feature relevance and translate model predictions into clinically actionable insights. 

*   **Primary Drivers:** Cross-model consensus identified the **Pelvis** (mean |SHAP| = 0.271) and **Hip** (mean |SHAP| = 0.188) as the primary kinematic drivers, accounting for over 95% of global model attribution.
*   **Minimal Contributors:** The Ankle contributed marginal predictive weight, while the Knee had zero attribution across all models.
*   **Biomechanics:** 
    *   *Pelvis Rigidity:* Lower pelvic entropy pushes model outputs toward stroke, reflecting a loss of movement complexity and rigid pelvic stabilization.
    *   *Hip Compensation:* Higher hip entropy drives stroke classification, indicating the use of extra hip movements to swing a weak leg forward.
*   **SHAP vs LIME:** Cross-validation between SHAP and LIME for the Cubic SVM model revealed near-perfect structural agreement, confirming that the learned boundaries capture true underlying biomechanical mechanisms.

## 🚀 Future Work
Future extensions of this project include:
*   Predicting continuous functional ambulatory scores (e.g., FAC, gait speed) rather than binary labels.
*   Incorporating multi-channel raw EMG signals to map neuromuscular activation patterns alongside joint kinematics.
*   Validating on multi-center gait datasets stratified by stroke lesion location and chronicity.

## 👏 Acknowledgments
Special thanks to mentors Dr. Debtanay Das (Assistant Professor, National Institute of Technology Silchar) and Dr. Arnab Sarmah (Researcher, Arblet Inc. / PhD, IIT Guwahati) for their invaluable guidance, expertise, and continuous encouragement throughout this internship.
