<h1>
  <span class="headline">MLOps Fundamentals</span>
  <span class="subhead">Implementing MLOps</span>
</h1>

Let's revisit the customer churn prediction example, focusing on describing the steps conceptually, emphasizing the MLOps principles involved, including elements of Continuous Delivery.
**Note**: In this table, "conceptual" means that we'll describe the purpose and general approach of each step at a high level, rather than providing detailed code. This highlights the underlying MLOps principles without getting bogged down in implementation specifics.

**Scenario:** Building a simplified pipeline to train and deploy a model that predicts customer churn, focusing on core MLOps concepts and incorporating a Continuous Delivery approach.

**Tools (Conceptual Use):**

-   **MLflow:** Experiment tracking, model registry, model deployment.
-   **Python:** Programming language.
-   **Scikit-learn:** Machine learning library.
-   **Flask:** Web framework for creating a simple model serving API.
-   **Git:** Version control.

**Updated Table Header:**

| Step                                      | Concept                                                                                                     | Description                                                                                                                                                                                                                                                                                                        | Tools                               |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| **1. Project Setup and Version Control**   | Establish a project structure and use Git to track all code changes.                                        | Create a project directory, initialize a Git repository, set up a virtual environment, and install necessary libraries. All code committed to the Git repository.                                                                                                              | Git, Python virtual environments   |
| **2. Data Preparation**                  | Load, clean, and preprocess the customer churn data. Emphasize data quality and versioning.                | Load the `churn_data.csv` file, handle missing values, transform features, and split into training and testing sets. Document these steps and emphasize tracking data changes (ideally with a data versioning tool).                                                                 | Pandas, DVC (optional)             |
| **3. Model Training and Experiment Tracking** | Train a machine learning model and use MLflow to track experiments, log parameters, and metrics.          | Write a Python script (`train.py`) to train a model. Use the MLflow Tracking API to log hyperparameters, metrics, and the trained model. Run multiple experiments with different hyperparameters, and MLflow tracks each run.                                                                 | Python, Scikit-learn, MLflow       |
| **4. Model Packaging**                   | Package the trained model using MLflow's standard model format for easy deployment.                        | Use `mlflow.sklearn.log_model()` to package the scikit-learn model. This creates a directory containing the serialized model, environment description, and metadata.                                                                                                              | MLflow                             |
| **5. Model Serving with Flask**          | Create a simple REST API using Flask to serve predictions.                                                 | Write a Python script (`serve.py`) to load the packaged model and define a `/predict` endpoint. Add a script (`deploy_to_staging.py`) for Continuous Delivery to a staging environment and a manual approval step to promote to production with another script (`promote_to_prod.py`). | Flask, MLflow, Python              |
| **6. Model Monitoring (Conceptual)**       | Monitor model performance and data drift after deployment.                                                 | Log model inputs and predictions, periodically re-calculate performance metrics, and compare them to training metrics. Visualize metrics over time using a dashboard and set alerts based on thresholds.                                                                                                               | Conceptual                         |
| **7. Model Retraining (Conceptual)**       | Periodically retrain the model with new data.                                                              | Automate retraining using a scheduler to trigger the `train.py` script. Use MLflow to track retrained model performance and select the best one.                                                                                                                                 | Conceptual                         |


**Discussion Points:**

-   **How does this simplified example illustrate the core principles of MLOps, including Continuous Delivery?** (Automation, version control, experiment tracking, model management, automated deployment to staging, manual promotion to production)
-   **What are the limitations of this simplified approach?** (Limited scalability, no robust monitoring, basic deployment, manual promotion step)
-   **How could this example be extended to a more production-ready MLOps pipeline?** (Using a workflow orchestrator, containerization, cloud deployment, more sophisticated monitoring tools, full CI/CD pipelines, potentially Continuous Deployment)
-   **How does the introduction of Continuous Delivery (with manual promotion) change the deployment process compared to the previous example?** (Adds a layer of control and validation before deploying to production)
-   **What are the trade-offs between Continuous Delivery and Continuous Deployment in this context?** (Control vs. speed, risk mitigation vs. automation)

