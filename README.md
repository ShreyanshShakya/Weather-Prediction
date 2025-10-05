# Weather Forecasting System: Scalable MLOps Pipeline for 4,000+ Cities

[![License](https://img.shields.io/github/license/ShreyanshShakya/Weather-Prediction)](LICENSE)
[![Docker](https://img.shields.io/badge/docker-ready-blue)]()

## Project Overview

This project provides a robust, scalable, machine-learning weather forecasting system that predicts the next day's average temperature for thousands of Indian cities. It demonstrates an end-to-end MLOps workflow, from massive data processing to live API and UI deployment, using XGBoost, containerization, and cloud-native services.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Project Workflow](#project-workflow)
- [Key Challenges & Solutions](#key-challenges--solutions)
- [Features](#features)
- [API Usage](#api-usage)
- [Live Demo](#live-demo)
- [Local Setup](#local-setup)
- [Contributing](#contributing)
- [Contact](#contact)

---

## Architecture

The system is composed of the following modular components:

1. **Data Source ([Kaggle Indian Weather Dataset](https://www.kaggle.com/datasets/mukeshdevrath007/indian-5000-cities-weather-data))**  
   - 90GB of historical weather data for 6,000+ Indian cities, mounted directly in Kaggle for efficient access.

2. **Model Factory (Kaggle Notebook)**  
   - Automated Python pipeline to discover, train, and batch-upload city-specific XGBoost models, making the process resumable and efficient.

3. **Model Registry ([Hugging Face Model Hub](https://huggingface.co/Shreyansh1718/Weather-prediction-model))**  
   - Centralized, versioned storage for thousands of trained model artifacts.

4. **Serving API (Hugging Face Docker Space)**  
   - Lightweight, scalable FastAPI server in Docker, dynamically loading models on demand and caching them for fast responses.

5. **Frontend UI ([Gradio Space](https://shreyansh1718-weather-prediction.hf.space))**  
   - User-friendly Gradio web interface, interacting live with the API for real-time forecasts.


---<img width="1686" height="694" alt="diagram-export-10-5-2025-12_01_14-PM" src="https://github.com/user-attachments/assets/8315ea06-20a5-4b97-a6ed-21e2760afdac" />


## Technology Stack

- **Data Science & ML:** Pandas, Scikit-learn, XGBoost  
- **MLOps & Backend:** FastAPI, Docker, Uvicorn, Hugging Face Hub  
- **Frontend:** Gradio  
- **Cloud Platforms:** Kaggle Notebooks, Hugging Face Spaces  
- **Language:** Python

---

## Project Workflow

This modern, decoupled architecture follows this workflow:

```
[Kaggle Dataset] 
   ↓
[Kaggle Training Pipeline] 
   ↓
[Hugging Face Model Hub] 
   ↓
[FastAPI Server] 
   ↓
[Gradio UI]
```

Each component is responsible for a specific stage:
- Data is ingested and processed in Kaggle
- Models are trained and registered in Hugging Face Hub
- Serving API fetches and caches models on demand
- Frontend UI provides live user interaction

---

## Key Challenges & Solutions

- **Resource Limits:**  
  - *Challenge:* Processing a 90GB dataset on free cloud platforms.  
  - *Solution:* Leveraged Kaggle's mounted storage for in-place ETL and training.

- **API Rate Limiting:**  
  - *Challenge:* Hitting Hugging Face Hub’s commit rate limits.  
  - *Solution:* Switched to batch uploading models, drastically reducing API calls.

- **Dependency Conflicts:**  
  - *Challenge:* Pre-installed libraries on Kaggle conflicting with project dependencies.  
  - *Solution:* Fully containerized the application with Docker for clean, reproducible environments.

- **Scalability:**  
  - *Challenge:* Serving thousands of individual models without huge server costs.  
  - *Solution:* On-demand model loading and intelligent caching in the API server.

---

## Features

- **Accurate Forecasting:**  
  - XGBoost Regressor trained per city for precise temperature prediction.

- **Automated, Resumable Training:**  
  - Robust pipeline skips already-trained models for efficiency.

- **Intelligent Feature Engineering:**  
  - Time-based, lag, and rolling-window features enhance prediction quality.

- **High-Performance API:**  
  - FastAPI provides async, validated, and documented endpoints.

- **Containerized, Reproducible Deployments:**  
  - Docker ensures consistent environments from development to production.

- **Live User Interface:**  
  - Gradio UI for friendly, interactive web access.

---

## API Usage

The deployed application exposes a single `/predict` endpoint.

**Endpoint:**  
`POST /predict`  
Send at least 7 days of historical weather data in JSON format.

**Sample Request:**
```bash
curl -X 'POST' \
  'https://your-hugging-face-space-url/predict' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "historical_data": [
      { "date": "2024-01-20", "temperature_2m": 15.5, "precipitation": 0.1, "month": 1, "day_of_year": 20, "day_of_week": 5, "year": 2024 },
      { "date": "2024-01-21", "temperature_2m": 16.0, "precipitation": 0.0, "month": 1, "day_of_year": 21, "day_of_week": 6, "year": 2024 },
      { "date": "2024-01-22", "temperature_2m": 15.8, "precipitation": 0.0, "month": 1, "day_of_year": 22, "day_of_week": 0, "year": 2024 },
      { "date": "2024-01-23", "temperature_2m": 16.2, "precipitation": 0.0, "month": 1, "day_of_year": 23, "day_of_week": 1, "year": 2024 },
      { "date": "2024-01-24", "temperature_2m": 16.5, "precipitation": 0.0, "month": 1, "day_of_year": 24, "day_of_week": 2, "year": 2024 },
      { "date": "2024-01-25", "temperature_2m": 16.8, "precipitation": 0.0, "month": 1, "day_of_year": 25, "day_of_week": 3, "year": 2024 },
      { "date": "2024-01-26", "temperature_2m": 17.0, "precipitation": 0.0, "month": 1, "day_of_year": 26, "day_of_week": 4, "year": 2024 }
    ]
}'
```

**Expected Response:**
```json
{
  "prediction_date": "2024-01-27",
  "predicted_avg_temp_celsius": 17.25
}
```
> For more details and interactive docs, visit the `/docs` endpoint of the deployed API.

---

## Live Demo

- [User Interface (Gradio Space)](https://shreyansh1718-weather-prediction.hf.space)

---

## Local Setup

1. **Clone the repository:**
   ```bash
   git clone https://github.com/ShreyanshShakya/Weather-Prediction.git
   cd Weather-Prediction
   ```
2. **Build and run with Docker:**
   ```bash
   docker build -t weather-prediction .
   docker run -p 8000:8000 weather-prediction
   ```
3. **Access the API at:**  
   [http://localhost:8000/docs](http://localhost:8000/docs)

---

## Contributing

Contributions, issues, and feature requests are welcome!  
Feel free to open an issue or submit a pull request.

---

## Contact

For questions or support, contact [ShreyanshShakya](https://github.com/ShreyanshShakya).
