# Bank Customer Churn Prediction App

An academic software project that combines a bank-customer churn model, a FastAPI backend, and a Next.js interface for individual and CSV batch predictions.

## Overview

The backend loads a pre-trained XGBoost classifier and its preprocessing artifacts. It validates individual customer inputs, applies the configured feature scaling and decision threshold, and returns a churn probability with per-feature SHAP contributions. The batch endpoint accepts a CSV and returns a prediction for each row; it omits per-row SHAP explanations. A separate endpoint provides chart data for the interface.

The frontend is organized into three views: an individual prediction form, CSV batch prediction, and model charts.

## Repository structure

```text
.
├── backend/
│   ├── app/
│   │   ├── routers/       # Individual, batch, and chart endpoints
│   │   ├── schemas/       # Pydantic request and response models
│   │   └── services/      # Prediction and chart logic
│   ├── artifacts/         # XGBoost model, scaler, config, sample CSV
│   ├── main.py            # FastAPI application
│   └── requirements.txt
└── churn-frontend/        # Next.js, React, and TypeScript UI
```

## Stack

- **API:** Python, FastAPI, Pydantic, Pandas
- **Model and explanations:** XGBoost, scikit-learn, SHAP, Joblib
- **Frontend:** Next.js 16, React 19, TypeScript, Tailwind CSS, Recharts
- **Deployment adapter:** Mangum is included for AWS Lambda integration

## Run locally

Run the API and frontend in separate terminals.

### 1. Start the API

```bash
cd backend
python -m venv .venv
```

Activate the environment, then install the dependencies and start FastAPI:

```bash
# macOS / Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1

pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

The API documentation is available at `http://localhost:8000/docs`. The model, scaler, and configuration files are expected in `backend/artifacts/` and are included in this repository.

### 2. Start the frontend

```bash
cd churn-frontend
npm install
```

The frontend defaults to `http://localhost:8000` for its API. To use a different API URL, create `churn-frontend/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:8000
```

Then start the development server:

```bash
npm run dev
```

Open `http://localhost:3000`.

## API

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/` | Basic service health response |
| `POST` | `/predict/single` | Predict churn for one customer and return SHAP contributions |
| `POST` | `/predict/batch` | Accept a CSV upload and return per-row predictions |
| `GET` | `/charts` | Return churn-distribution and feature-importance data |

For batch predictions, the CSV must contain the features listed in `backend/artifacts/config_modelo.json`: `age`, `products_number`, `balance`, `active_member`, `estimated_salary`, `credit_score`, `tenure`, and `credit_card`. A sample file is provided at `backend/artifacts/clientes_ejemplo.csv`.

## Model notes

The checked-in configuration identifies the classifier as XGBoost, scales six continuous inputs with a `RobustScaler`, and uses a decision threshold of `0.53` selected against F1. Individual predictions include SHAP contributions; batch predictions intentionally do not calculate SHAP for every row.

The project documentation reports test-set results of **F1 0.597, recall 0.603, and ROC-AUC 0.849**. These are the recorded results for this project and should be interpreted in the context of its dataset and evaluation split; they are not a guarantee of performance on new data.

The repository contains the inference application and model artifacts. It does not contain the model-training notebook or deployment infrastructure. Although a Mangum handler is available, this README documents local execution rather than a verified cloud deployment.

## Notes

This project is for learning and portfolio review. Predictions are model outputs and are not intended as financial or customer-retention decisions.

## Source history

When reviewed, the application files in this repository matched the source-tree snapshot in [Xunni1e/proyecto-final-ml-churn-banacario](https://github.com/Xunni1e/proyecto-final-ml-churn-banacario), which retains the earlier development commits. That history includes a backend refactor by Juan Felipe Plata for Lambda integration, lazy SHAP initialization, and dependency packaging. This repository keeps the same application snapshot under Juanxo17 with this project README.
