
# End-to-End Weather Forecasting System: A Scalable MLOps Pipeline for 4,000+ Cities

This repository contains the complete code for an end-to-end machine learning project that predicts the next day's average temperature. The project trains an XGBoost model on historical weather data and deploys it as a robust, live user interface.

This project demonstrates automated model training, scalable API deployment, and a live user interface, moving from a 90GB Kaggle dataset to a dynamic, on-demand prediction service.

# The Architecture

 1. Data Source ([Kaggle](https://www.kaggle.com/datasets/mukeshdevrath007/indian-5000-cities-weather-data): The original 90GB dataset of historical  weather data for over 6,000 Indian cities, hosted on Kaggle.

 2. The Model Factory (Kaggle Notebook): A robust Python pipeline  that runs on Kaggle's free compute. It automatically:

   - Discovers all city data files.

   - Checks for already trained models to be resumable.

   - Trains a specialized XGBoost model for each new city.

   - Batches the finished models to avoid API rate limits.

   - Uploads the model artifacts to the Model Hub.

3. The Model Registry ([Hugging Face Model Hub](https://huggingface.co/Shreyansh1718/Weather-prediction-model): A central repository (Shreyansh1718/Weather-prediction-model) that stores thousands of individual, version-controlled models, one for each city.

4. The Serving API (Hugging Face Docker Space): A lightweight, scalable FastAPI server running in a Docker container. It serves predictions on-demand:

- When a request for a city arrives, it downloads the corresponding model from the Model Hub.

- It caches the model in memory for subsequent fast predictions.

5. The Frontend UI ([Hugging Face Gradio Space](https://shreyansh1718-weather-prediction.hf.space): A separate, interactive web application built with Gradio. It provides a user-friendly interface that calls the live API to deliver forecasts.

# Technology Stack 

- Data Science & ML: Pandas, Scikit-learn, XGBoost

- MLOps & Backend: FastAPI, Docker, Uvicorn, Hugging Face Hub (Model Registry & Git-based deployment)

- Frontend: Gradio

- Cloud Platforms: Kaggle Notebooks, Hugging Face Spaces

- Languages: Python

# Project Workflow
This project is built on a modern, decoupled architecture, where each component has a specific job. This is how data flows through the system:

[Kaggle Dataset] -> [Kaggle Training Pipeline] -> [HF Model Hub] -> [FastAPI Server] -> [Gradio UI]

1. Data Source ([Kaggle](https://www.kaggle.com/datasets/mukeshdevrath007/indian-5000-cities-weather-data): The process starts with the 90GB "Indian 5000 Cities Weather Data" dataset, which is accessed directly within the Kaggle environment to ensure maximum speed and avoid resource limits.

2. The Model Factory (Kaggle Notebook): A robust Python script runs in a Kaggle Notebook to act as an automated training pipeline. For each of the 6,000+ cities, it:

- Checks if a model for that city already exists in the Model Hub to make the process resumable.

- Trains a specialized XGBoost model on that city's historical data.

- Saves the trained model and its metadata locally.

3. The Model Registry (Hugging Face Model Hub): After a batch of models is trained, the pipeline uploads all the model artifacts to a central repository on the Hugging Face Hub (Shreyansh1718/Weather-prediction-model). This acts as our version-controlled "Model Registry."

4. The Serving API (Hugging Face Docker Space): A lightweight, scalable FastAPI server runs in a Docker container on a separate Hugging Face Space. It serves predictions on-demand:

- When a request for a city arrives (e.g., /predict/jaipur), it downloads the appropriate model from the Model Hub.

- It caches the model in memory for future fast predictions.

5. The Frontend UI (Hugging Face Gradio Space): A separate, interactive web application built with Gradio provides the user interface. It calls the live API to fetch forecasts and display them to the user.
  

# Key Challenges and Solutions
  
- Challenge: Resource Limits: The 90GB dataset was too large to download or process in memory on free cloud platforms.

 - Solution: I engineered an ETL and Training pipeline directly within the Kaggle environment, leveraging the pre-mounted dataset to avoid download and memory issues.

 - Challenge: API Rate Limiting: The initial training pipeline hit the Hugging Face Hub's rate limit of 128 commits/hour.

 - Solution: I re-architected the pipeline to use a batch upload strategy. The script now trains all models for a given batch locally and then performs a single, efficient upload, reducing hundreds of API calls to just one.

- Challenge: Dependency Conflicts: The Kaggle and other cloud environments had pre-installed libraries that conflicted with the project's dependencies.

- Solution: I containerized the application using Docker, creating a clean, isolated, and reproducible environment that completely solved all dependency issues.

- Challenge: Scalability: Serving thousands of individual models would require a massive server.

- Solution: I designed a scalable, on-demand API that only loads models from the Hub when they are first requested and then caches them in memory, resulting in a lightweight and efficient server.



# ✨ Key Features

- Accurate Forecasting: Utilizes an XGBoost Regressor, a powerful gradient-boosting model.

- Intelligent Feature Engineering: Creates predictive features from historical data, including time-based attributes, lag features, and rolling window averages.

- High-Performance API: Built with FastAPI, providing high performance, automatic data validation, and self-generating interactive documentation.

- Containerized and Reproducible: The entire application is containerized with Docker, guaranteeing a consistent environment for both development and deployment.

- Automated Training on Deploy: The Docker container automatically runs the training script upon deployment, ensuring the model artifacts are always perfectly compatible with the server environment.


# 🔄 Project Workflow

1. The project follows a complete MLOps workflow automated within the Docker container:

2. Data Preparation: The train_model.py script loads historical daily weather data for a specific city from a local CSV file.

3. Feature Engineering: The script creates new, predictive features from the time-series data to improve model performance.

4. Model Training: An XGBoost model is trained on the engineered features.

5. Artifact Generation: The script saves the trained model (.json) and the list of feature columns (.json) to disk.

6. API Serving: The app.py script loads these freshly generated artifacts into a FastAPI application, which exposes a /predict endpoint.

7. Containerization: The Dockerfile defines the environment, runs the training script, and starts the FastAPI server, packaging the entire process into a single, portable image.

# ⚡ API Usage
The deployed application provides a single endpoint for predictions: /predict. You can interact with it using any HTTP client or through the automatic documentation provided by FastAPI at the /docs endpoint.

**Example curl Request**

To get a prediction, send a POST request with at least 7 days of historical weather data.


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
**Expected Response**
``` json
{
  "prediction_date": "2024-01-27",
  "predicted_avg_temp_celsius": 17.25
}
```

# Live Demo

[User Interface](https://shreyansh1718-weather-prediction.hf.space)

