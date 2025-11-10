## for data ingestion, run this is powershell, but before this ensure that you have the valid service accounts and its keys (json file) downloaded in your local machine:
    $env:GOOGLE_APPLICATION_CREDENTIALS="C:\Users\Amit\Downloads\gen-lang-client-0792134177-6b57cb5b683f.json"

## --------- SUPER-DETAILED PROJECT README BELOW (auto-generated for project completeness) ----------

# Hotel Reservation Prediction (MLOps Project)

## Project Overview
A comprehensive Machine Learning Operations (MLOps) project for predicting hotel reservation outcomes (cancellation or not) using advanced ML techniques with a full-featured engineering, training, evaluation, and deployment pipeline. The project integrates robust data processing, model selection, REST and streamlit applications, CI/CD with Jenkins/Docker, and cloud deployment support.

---

## 1. Tech Stack & Dependencies
- **Language:** Python 3.10+
- **ML Libraries:** scikit-learn, lightgbm, xgboost, imbalanced-learn, statsmodels, pandas, numpy, seaborn, matplotlib
- **Cloud & Automation:** Google Cloud Platform (GCS/Cloud Run), MLflow, Jenkins, Docker
- **Web Apps:** Flask (REST), Streamlit (UI)
- **Notebook/Visualization:** jupyter/ipykernel
- **Other:** PyYAML (for config), joblib (model persistence)

Install dependencies with:
```bash
pip install -r requirements.txt
```

---

## 2. Project Structure
- `notebook/notebook.ipynb`: All data exploration, visualization, and EDA+ML prototyping
- `src/`: Modular code for ingestion, preprocessing, training, logging, and custom exceptions
- `pipeline/training_pipeline.py`: Orchestrates ingestion, processing, and training
- `config/`: YAML & Python configs for pipeline paths and model parameters
- `artifacts/`:
    - `raw/`: Downloaded/raw and split input data
    - `processed/`: Processed and engineered datasets
    - `models/`: Model checkpoints
- `templates/`, `static/`: Flask frontend
- `logs/`: Logs for all experiments and pipeline steps
- `Dockerfile`, `Jenkinsfile`: For deployment and automation

---

## 3. Data and Pipeline
### Data Sources
- Downloaded via GCP bucket (see config)
- Contains hotel booking records (features: guest, booking, and stay details; target: cancellation)

### Data Schema Example
- `lead_time`, `no_of_special_requests`, `avg_price_per_room`, ...
- Target: `booking_status` (0 = cancelled, 1 = not cancelled)

### Full Pipeline Overview
1. **Data Ingestion**: Download from GCP bucket, split to train/test
2. **Preprocessing**: Drop unused columns (e.g., IDs), label encode categoricals, handle skewed numerical features, remove duplicates
3. **Class Balancing**: Use SMOTE for balanced classes
4. **Feature Selection**: RandomForest on train data picks most-important features
5. **Model Training**: LightGBM (tuned via randomized search), scoring, MLflow logging
6. **Model Serving**: Saved model is used in Flask & Streamlit apps for online prediction
7. **Deployment**: CI/CD to GCP Cloud Run via Docker/Jenkins

Configurable via `config/config.yaml`

---

## 4. Jupyter Notebook: Deep Dive
**File:** `notebook/notebook.ipynb`

### Notable Steps & Outputs:
**1. Data Loading:**
- Loads raw data, inspects columns, drops identifiers (`Unnamed: 0`, `Booking_ID`)
- Checks shape, nulls, and duplicates (removes as required)

**2. Exploratory Data Analysis (EDA):**
- **Univariate Analysis:** Custom plotting for all numerical features
- **Categorical Distributions:** Bar charts for all categorical columns, e.g. `room_type_reserved`, `market_segment_type`
- **Class Imbalance:** `booking_status` distribution (target); reveals imbalance in the dataset

**3. Bivariate Analysis:**
- Boxplots: Numerical features vs. `booking_status`
- Countplots: Categorical features vs. `booking_status`

**4. Data Preparation:**
- Label encoding for categoricals, mapping stored for reproducibility
- Null, duplicates, skewness checks
- Log-transform heavy skewed columns
- Feature importances with RandomForest to shortlist top predictive variables
- SMOTE balancing for equal representation of classes

**5. Model Building & Evaluation:**
- Several models compared (Logistic Regression, SVM, RandomForest, XGBoost, LightGBM, etc)
- Metrics: Accuracy, Precision, Recall, F1
- Hyperparameter tuning for top models (RandomizedSearchCV for Random Forest, LightGBM)
- LightGBM chosen for deployment (performance/size tradeoff, checkpointed)

**6. Pipeline Integration:**
- Final feature set and model are saved for inference pipeline
- Demonstrated prediction on new sample data in-cell

> **To rerun notebook end-to-end:**<br>
> - Install requirements (`pip install -r requirements.txt`)
> - Place `train.csv` in `notebook/`
> - Start with all cells; outputs appear inline (supports interactive plotting)

---

## 5. Running the Pipeline (Manual & Automated)

### Manual, Step-by-Step
```bash
# 1. Set Google credentials (replace path with your key):
$env:GOOGLE_APPLICATION_CREDENTIALS="C:\Users\Amit\Downloads\gen-lang-client-0792134177-6b57cb5b683f.json"

# 2. Run full data pipeline (download, preprocess, train)
python pipeline/training_pipeline.py
```

### As Docker Container
```bash
docker build -t hotel-reservation-ml .
docker run -p 8080:8080 hotel-reservation-ml
```

### With CI/CD (via Jenkins Pipeline)
- Includes build/push to GCR, deploy to Cloud Run (see `Jenkinsfile`)

---

## 6. Model Inference & Web Apps
- **Flask App:** (in `app.py`) Exposes REST/web form for predictions on top-10 features. 
- **UI:** (HTML/CSS in `templates/`, `static/`) Clean Bootstrap UI for user interaction. Demo fields clearly map to the processed model’s expected features.
- **Streamlit:** (in `streamlit_app.py`) For modern dashboards (if implemented).

---

## 7. Configuration & Customization
- **Pipeline parameters:** `config/config.yaml`, `config/model_params.py`
- **Paths:** Defined in `config/paths_config.py`
- **To change features/columns:** Update yaml + notebook if data schema changes

---

## 8. Artifacts, Logging & MLflow
- Datasets, model checkpoints saved in `artifacts/`
- All pipeline steps and errors logged in `logs/`
- MLflow runs, metrics, models in `mlruns/`

---

## 9. Deployment Guide
- Dockerfile and Jenkinsfile provided
- Out-of-the-box deployment to Google Cloud Run
- Can be adapted to AWS/GCP/Heroku as needed
- Add your secret/keys as per the guide

---

## 10. Reproducibility & Extension
- All logic is modular and config-driven
- Jupyter notebook = full experiment reproducibility
- Easily extendable for new ML models, new data, or new cloud providers

---

## 11. FAQ & Troubleshooting
- **Dependency Issues**: Always use requirements.txt (or editable pip install from `setup.py`)
- **GCP Auth Fails**: Ensure service account and valid key path
- **Model Input Error**: Columns in test/new sample must match processed_train.csv
- **Jenkins Error**: Ensure Docker/GCP credentials configured properly in Jenkins

---
