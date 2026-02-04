<h1>
  <span class="headline">MLOps Fundamentals</span>
  <span class="subhead">Implementing MLOps: A Practical Example</span>
</h1>

## **Overview**

Now that you understand MLOps principles and tools, it's time to **apply them in a real-world scenario**. In this hands-on lesson, you'll modify an existing ML pipeline to incorporate **MLOps best practices**.

### **Learning Objectives**

By the end of this microlesson, you will:

- **Analyze** an ML training pipeline and identify missing MLOps components.
- **Modify** the pipeline to include experiment tracking, versioning, and automation.
- **Use MLflow** to track experiments, register models, and support deployment workflows.

---

## **1. Understanding the Existing Pipeline**

You’ve been given a basic ML training script, but it lacks:

- **Experiment tracking** (no way to compare different runs).
- **Model versioning** (no structured model storage).
- **Automated deployment** (manual model saving/loading).

### **Before: Original ML Pipeline**

```python
import joblib
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_iris

iris = load_iris()
X = iris.data
y = iris.target

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

print(f"Training data shape: X_train={X_train.shape}, y_train={y_train.shape}")
print(f"Testing data shape: X_test={X_test.shape}, y_test={y_test.shape}")

print("Training model...")
model = RandomForestClassifier(random_state=42)
model.fit(X_train, y_train)
print("Model training complete.")

accuracy = model.score(X_test, y_test)
print(f"Model accuracy on test set: {accuracy:.4f}")

model_filename = "model.pkl"
joblib.dump(model, model_filename)
print(f"Model saved to {model_filename}")

```

---

## **2. Hands-On: Adding MLOps Best Practices**

### **Step 1: Add Experiment Tracking with MLflow**

Modify the script to wrap the training process in an MLflow run, log parameters and metrics, and log the trained model as an artifact.

```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_iris
from sklearn.metrics import accuracy_score

print("Loading data...")
iris = load_iris()
X = iris.data
y = iris.target
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

print(f"Training data shape: X_train={X_train.shape}, y_train={y_train.shape}")
print(f"Testing data shape: X_test={X_test.shape}, y_test={y_test.shape}")
print("-" * 30)

mlflow.set_experiment("MLOps Fundamentals - Iris")

with mlflow.start_run() as run:
    run_id = run.info.run_id
    print(f"Starting MLflow Run ID: {run_id}")
    print("-" * 30)

    n_estimators = 100
    max_depth = 5
    random_state = 42

    print("Logging parameters...")
    mlflow.log_param("n_estimators", n_estimators)
    mlflow.log_param("max_depth", max_depth)
    mlflow.log_param("random_state", random_state)
    mlflow.log_param("test_size", 0.3)
    mlflow.log_param("model_type", "RandomForestClassifier")

    print("Training model...")
    model = RandomForestClassifier(
        n_estimators=n_estimators,
        max_depth=max_depth,
        random_state=random_state
    )
    model.fit(X_train, y_train)
    print("Model training complete.")

    print("Evaluating model...")
    y_pred = model.predict(X_test)
    accuracy = accuracy_score(y_test, y_pred)
    print(f"Model accuracy on test set: {accuracy:.4f}")

    print("Logging metrics...")
    mlflow.log_metric("accuracy", accuracy)

    registered_model_name = "IrisRandomForestPipeline"
    print(f"Logging and registering model as '{registered_model_name}'...")
    mlflow.sklearn.log_model(
        sk_model=model,
        artifact_path="model",
        registered_model_name=registered_model_name
    )
    print("Model logged and registered.")
    print("-" * 30)

print("MLflow run completed.")
print(f"Run ID: {run_id}")
print(f"To view the run and registered model:")
print(f"1. Open your terminal in the directory where you ran this script.")
print(f"2. Run the command: mlflow ui")
print(f"3. Open your browser to http://localhost:5000 (or the address shown)")
print("-" * 30)

```

✅ **Now, each run is logged, making it easier to compare models.**

---

### **Step 2: Implement Model Versioning**

Now, take the model artifact logged in the previous step's run and register it in the MLflow Model Registry. This assigns it a version under a specific model name.

```python
# (Add this code block after the previous one, or run it separately
# ensuring 'current_run_id' holds the correct Run ID)

import mlflow

# --- Register the logged model ---
# Construct the URI pointing to the model artifact within the specific run
model_uri = f"runs:/{current_run_id}/model"
registered_model_name = "IrisRandomForestPipeline" # Choose a name for your model in the registry

print(f"Registering model from URI: {model_uri}")
print(f"Registering under name: {registered_model_name}")

try:
    # Register the model artifact found at the URI
    registered_model_version = mlflow.register_model(
        model_uri=model_uri,
        name=registered_model_name
    )
    print(f"Model registered successfully as Version: {registered_model_version.version}")
    print(f"Model Name: {registered_model_version.name}")

except Exception as e:
    print(f"Error registering model: {e}")
    print("Ensure the Run ID is correct and MLflow Tracking server is accessible.")

print("-" * 30)
print(f"To view the registered model:")
print(f"1. Run 'mlflow ui' in your terminal.")
print(f"2. Navigate to the 'Models' tab in the MLflow UI (http://localhost:5000).")
print("-" * 30)
```

✅ **Now, specific model artifacts are formally versioned and centrally managed in the MLflow Model Registry.**

---

### **Step 3: Prepare for Deployment**

Modify deployment scripts or applications (e.g., a separate deploy.py or an API server) to load models directly from the MLflow Model Registry using the registered model name and desired version or stage (e.g., "Production", "Staging", or a specific version number).

```python
import mlflow.pyfunc
import pandas as pd # Example for creating prediction input

model_uri = "models:/IrisRandomForestPipeline/Production"

print(f"Loading model from Model Registry: {model_uri}")
try:
    model = mlflow.pyfunc.load_model(model_uri)
    print("Model loaded successfully.")

    # Example Prediction (using placeholder data matching Iris features)
    # Replace with actual input data format for your application
    sample_data = pd.DataFrame([[5.1, 3.5, 1.4, 0.2]], columns=['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)'])
    print(f"\nSample input data:\n{sample_data}")

    predictions = model.predict(sample_data)
    print(f"\nPrediction: {predictions}")

except Exception as e:
    print(f"Error loading model or predicting: {e}")
    print("Ensure the model name and stage/version exist and MLflow Tracking Server is accessible.")
```

✅ Now, deployment processes can fetch approved, versioned models directly from the registry, eliminating manual file handling and reducing errors.

---

## **3. Key Takeaways**

✅ MLOps **improves ML workflows** by adding automation and tracking.\
✅ MLflow enables **experiment logging, model versioning, and easy deployment**.\
✅ Structured MLOps pipelines improve **scalability, reliability, and team collaboration**.

---

## **Bonus Challenge: Deploy Your Model for Public Access**

Want to **share your `IrisRandomForestPipeline` model online**? Try deploying it using **FastAPI + Docker + Render (or Hugging Face Spaces)**. This turns your trained model into a live service others can interact with.

### **Steps:**

1.  **Create an API with FastAPI:**
    *   Create a new Python file (e.g., `api.py`).
    *   This script will load the model from the MLflow Model Registry and define an endpoint to receive data and return predictions.
    *   **Important:** Before running this API, ensure you have promoted the desired version of your `IrisRandomForestPipeline` model to the `Production` stage in the MLflow UI.

    ```python
    # api.py
    from fastapi import FastAPI, HTTPException
    from pydantic import BaseModel, Field
    import mlflow.pyfunc
    import pandas as pd
    import os

    # Define the expected input data structure using Pydantic
    # Matches the Iris dataset features
    class IrisInput(BaseModel):
        sepal_length: float = Field(..., alias='sepal length (cm)')
        sepal_width: float = Field(..., alias='sepal width (cm)')
        petal_length: float = Field(..., alias='petal length (cm)')
        petal_width: float = Field(..., alias='petal width (cm)')

        class Config:
            allow_population_by_field_name = True # Allows using original feature names

    app = FastAPI(title="Iris Model API", description="API for Iris flower classification using MLflow model")

    # --- Load Model ---
    # Set MLflow tracking URI if it's not local or default
    # os.environ["MLFLOW_TRACKING_URI"] = "http://your-mlflow-server:5000"

    model_name = "IrisRandomForestPipeline"
    model_stage = "Production" # Load the model promoted to Production
    model_uri = f"models:/{model_name}/{model_stage}"

    print(f"Loading model '{model_name}' stage '{model_stage}' from {model_uri}...")
    try:
        model = mlflow.pyfunc.load_model(model_uri)
        print("Model loaded successfully.")
    except Exception as e:
        print(f"FATAL: Could not load model. Error: {e}")
        # In a real app, you might exit or disable the endpoint
        model = None # Set model to None if loading fails

    @app.on_event("startup")
    async def startup_event():
        if model is None:
            print("WARNING: Model not loaded. Prediction endpoint will not work.")

    @app.post("/predict/")
    async def predict(data: IrisInput):
        """
        Receives Iris features and returns the predicted class (0, 1, or 2).
        Send JSON like:
        {
          "sepal length (cm)": 5.1,
          "sepal width (cm)": 3.5,
          "petal length (cm)": 1.4,
          "petal width (cm)": 0.2
        }
        """
        if model is None:
            raise HTTPException(status_code=503, detail="Model is not available")

        try:
            # Convert Pydantic model to DataFrame expected by mlflow.pyfunc model
            # Ensure column names match exactly what the model was trained on
            input_df = pd.DataFrame([data.dict(by_alias=True)]) # Use by_alias=True for correct names
            print(f"Received input data: \n{input_df}")

            # Make prediction
            prediction = model.predict(input_df)

            # Prediction might be a numpy array, convert to standard Python type
            result = prediction[0].item() if hasattr(prediction[0], 'item') else prediction[0]

            print(f"Prediction result: {result}")
            return {"predicted_class": result}

        except Exception as e:
            print(f"Prediction error: {e}")
            raise HTTPException(status_code=400, detail=f"Error processing prediction: {e}")

    @app.get("/")
    async def read_root():
        return {"message": f"Welcome to the Iris Prediction API. Use the /predict/ endpoint (POST) to classify flowers. Model loaded: {'Yes' if model else 'No'}"}

    ```
    *   You'll also need `requirements.txt` including `fastapi`, `uvicorn`, `mlflow`, `scikit-learn`, `pandas`, `pydantic`.
    *   Run locally: `uvicorn api:app --reload`

2.  **Containerize it with Docker:**
    *   Create a `Dockerfile`:

    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.9-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the requirements file into the container at /app
    COPY requirements.txt .

    # Install any needed packages specified in requirements.txt
    # Add --no-cache-dir to reduce image size
    RUN pip install --no-cache-dir -r requirements.txt

    # Copy the API script into the container at /app
    COPY api.py .

    # Make port 8000 available to the world outside this container
    EXPOSE 8000

    # Define environment variable (optional, if needed)
    # ENV MLFLOW_TRACKING_URI="http://host.docker.internal:5000" # Example for local MLflow from Docker

    # Run api.py when the container launches using uvicorn
    # Use 0.0.0.0 to make it accessible from outside the container
    CMD ["uvicorn", "api:app", "--host", "0.0.0.0", "--port", "8000"]
    ```
    *   Build the image: `docker build -t iris-ml-api .`
    *   Run the container: `docker run -p 8000:8000 --name iris-api iris-ml-api` (Add `-e MLFLOW_TRACKING_URI=...` if your MLflow server isn't the default local one and needs to be accessed from the container).

3.  **Deploy on Render, Hugging Face Spaces, or another cloud service.**
    *   These platforms often integrate directly with Docker containers.
    *   **Render:** Connect your Git repository, choose "Web Service," select Docker, and configure port 8000. Set environment variables (like `MLFLOW_TRACKING_URI` if needed) in the Render dashboard.
    *   **Hugging Face Spaces:** Choose "Docker" space type, push your code (including `Dockerfile` and `api.py`) to the Space's repository. Hugging Face automatically builds and runs the container. Configure secrets for environment variables.

Once deployed, you'll get a **public URL**. You can send POST requests to `YOUR_PUBLIC_URL/predict/` with the Iris data in JSON format to get real-time predictions from your MLflow-managed model! 🎯
