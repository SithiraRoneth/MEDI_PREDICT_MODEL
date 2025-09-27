# 🏥 Clinical Document Classifier (Machine Learning Group Project)

A production-grade **clinical document classification system** for **MedArchive Solutions**.  
It classifies medical transcriptions into the correct specialty (e.g., Cardiovascular, Neurology) using **TF-IDF + Logistic Regression**, and provides a **REST API** for predictions.  
Includes EDA, modeling, clustering, evaluation, and deployment steps.

---

## 📂 Project Structure
```

clinical-doc-classifier/
├─ notebooks/  # Jupyter notebooks for each phase
│  ├─ 1\_eda\_and\_preprocessing.ipynb
│  ├─ 2\_classification\_modeling.ipynb
│  └─ 3\_clustering\_analysis.ipynb
|
├─ artifacts/ # Saved models
│  └─ model.joblib
|
├─ requirements.txt # Dependencies
└─ README.md # This file

````

---

## ⚙️ Setup

1. Clone the repo and open the project folder:
```powershell
   cd clinical-doc-classifier
````

2. Create virtual environment (Windows):

```powershell
   python -m venv venv
   .\venv\Scripts\activate
```

3. Install dependencies:

```powershell
   pip install -r requirements.txt
```

---

## 📊 Phase 1: EDA, Modeling & Clustering

Run Jupyter Notebook:

```powershell
  jupyter notebook
```

Then open:

* `1_eda_and_preprocessing.ipynb` → text cleaning, TF-IDF, class distribution.
* `2_classification_modeling.ipynb` → Logistic Regression training, cross-validation, evaluation.
* `3_clustering_analysis.ipynb` → k-Means clustering, silhouette score, top terms per cluster.

---

## 🖥️ Phase 1 (script version)

### Train the model

```powershell
  python src/train.py
```

This creates `artifacts/model.joblib`.

### Evaluate model

```powershell
  python src/eval.py
```

Prints classification report & confusion matrices.

### Run clustering

```powershell
  python src/clustering.py
```

Shows silhouette scores, top words per cluster, and distribution.

---

## 🌐 Phase 2: REST API (Local)

Start API server:

```powershell
  python src/serve_predict.py
```

Test with PowerShell:

```powershell
  Invoke-RestMethod -Uri "http://127.0.0.1:8080/predict" -Method POST -ContentType "application/json" -Body '{"text":"Patient presents with severe chest pain and shortness of breath."}'
```

Example response:

```json
  {
    "prediction": "Cardiovascular / Pulmonary",
    "probabilities": {
      "Cardiovascular / Pulmonary": 0.89,
      "Neurology": 0.04,
      "Orthopedic": 0.02
    }
  }
```

---

## ☁️ Phase 2: Deployment on Google Cloud Vertex AI

1. Enable **Vertex AI API** in your GCP project.

2. Create a GCS bucket and upload artifacts:

```powershell
   gsutil mb -l us-central1 gs://<YOUR_BUCKET>
   gsutil cp artifacts/model.joblib requirements.txt gs://<YOUR_BUCKET>/model/
```

3. Deploy with Vertex AI prebuilt container for scikit-learn:

```powershell
   gcloud ai models upload --region=us-central1 ^
       --display-name="clinical-doc-classifier" ^
       --artifact-uri="gs://<YOUR_BUCKET>/model/" ^
       --container-image-uri="us-docker.pkg.dev/vertex-ai/prediction/sklearn-cpu.1-6:latest"

   gcloud ai endpoints create --region=us-central1 --display-name="clinical-endpoint"

   gcloud ai endpoints deploy-model ENDPOINT_ID --region=us-central1 --model=MODEL_ID ^
       --traffic=100 --machine-type="n1-standard-2"
```

4. Send test prediction with Python:

```python
   from google.cloud import aiplatform
   aiplatform.init(project="YOUR_PROJECT", location="us-central1")
   endpoint = aiplatform.Endpoint("ENDPOINT_ID")
   res = endpoint.predict(instances=[{"text": "Patient presents with chest pain"}])
   print(res)
```

---

## 📑 Deliverables

* **Notebooks** → EDA, modeling, clustering
* **Artifacts** → `model.joblib`
* **Scripts** → training, evaluation, clustering, REST API
* **Report** → `reports/technical_report_template.md`
* **Presentation** → slides for stakeholders

---

## 👩‍💻 Authors

* Piumini Rashmika & Team
  IJSE | HDSE 67/68 | Semester 4, 2025

