# 📂 Databricks CSV Uploader App

A lightweight, memory-optimized Streamlit application built to run natively within **Databricks Apps**. This utility allows data engineers and analysts to easily upload, preview, and stream CSV files directly into a targeted **Unity Catalog Volume** without inflating server memory or leaking sensitive platform credentials.

---

## 🚀 Features

* 
**Direct Volume Streaming:** Uses the native `databricks-sdk` to stream file blocks directly into Unity Catalog Volumes, bypassing memory bottlenecks for larger files.


* **Intelligent Sampling:** Safely parses and profiles a top-chunk data preview (`nrows=1000`) to analyze file structures and data types before executing an upload.
* **Dynamic Path Sanitization:** Normalizes catalog, schema, volume, and optional subfolder configurations safely to prevent structural directory errors.
* **Zero-Config Authentication:** Inherits workspace host context and authentication implicitly when deployed as a Databricks App via a managed service identity.

---

## 📁 Repository Structure

```text
├── app.py             # Streamlit application core logic
├── app.yaml           # Databricks App deployment and runtime configuration
└── requirements.txt   # Core Python dependencies

```

---

## 🛠️ Configuration & Deployment

### 1. Application Configuration (`app.yaml`)

The application defines a custom configuration mapping port `8000` to bind seamlessly with the Databricks Apps runtime environment. It also suppresses telemetry to maximize privacy:

```yaml
command:
  - streamlit
  - run
  - app.py
  - --server.port
  - "8000"
  - --server.address
  - "0.0.0.0"
env:
  - name: STREAMLIT_GATHER_USAGE_STATS
    value: "false"

```

### 2. Dependencies (`requirements.txt`)

The backend is driven by the following core libraries:

```text
streamlit>=1.30.0
pandas>=2.0.0
databricks-sdk>=0.20.0

```

---

## 💻 Local Development Setup

To run and test the application interface locally before deploying it to your Databricks workspace:

1. **Clone the repository:**
```bash
git clone <repository-url>
cd csv-uploader-app

```


2. **Set up a virtual environment:**
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`

```


3. **Install dependencies:**
```bash
pip install -r requirements.txt

```


4. **Configure Databricks CLI Authentication:**
Ensure you have a local Databricks CLI profile configured or your environment variables exported so the SDK can communicate with your workspace:
```bash
export DATABRICKS_HOST="https://<your-workspace-url>.cloud.databricks.com"
export DATABRICKS_TOKEN="dapi****************"

```


5. **Run the app:**
```bash
streamlit run app.py

```



---

## 🔒 Security & RBAC Requirements

For successful file ingestion, ensure that the identity running the app (or your personal token during local testing) has been granted the minimum required data permissions in Unity Catalog:

* **`USE CATALOG`** on the destination catalog.
* **`USE SCHEMA`** on the destination schema.
* **`WRITE VOLUME`** on the targeted Unity Catalog Volume.

---

## 💡 Quick Post-Upload Query Guide

Once the app successfully pushes a file to your volume, you can immediately query it directly from a Databricks SQL Warehouse or Notebook using standard SQL notation:

```sql
SELECT * FROM csv.`/Volumes/your_catalog/your_schema/your_volume/your_file.csv`;

```Volume
