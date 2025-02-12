<h1>
  <span class="headline">MLOps Fundamentals</span>
  <span class="subhead">MLOps Tools and Technologies</span>
</h1>

Let's explore the landscape of MLOps tools, focusing on categories relevant to our simplified approach without Kubernetes and emphasizing readily accessible options like MLflow.

### A. Orchestration and Workflow Management

These tools help automate and manage the execution of ML pipelines.

-   **Apache Airflow, Prefect, Luigi:** Powerful tools for complex workflows, but we'll focus on simpler automation using Python scripts and basic scheduling for this introductory class.
    -   **Conceptual Role:** Orchestrating the different steps in an ML pipeline (data preprocessing, model training, evaluation, etc.). Defining dependencies between tasks and scheduling their execution.

**For this class:** We will emphasize the *principles* of orchestration (task dependencies, scheduling) without using these specific tools. We can illustrate these principles with simple Python scripts and discuss how they *could* be scheduled with tools like `cron`.

### B. CI/CD for Machine Learning

These tools automate the building, testing, and deployment of ML models and code.

-   **Jenkins, GitLab CI/CD, CircleCI, Azure DevOps, Google Cloud Build, AWS CodePipeline:** Popular CI/CD platforms that can be used to automate various stages of the ML lifecycle.
    -   **Conceptual Role:** Automating the testing of code and models whenever changes are made, and potentially automating the deployment process.

**For this class:** We will focus on the *principles* of CI/CD (automated testing, version control) and how they apply to machine learning without implementing a full CI/CD pipeline. We'll emphasize the difference between Continuous Delivery and Continuous Deployment, as outlined earlier.

### C. Model Training and Experiment Tracking

These tools help manage and track the model training process.

-   **MLflow:**
    -   An open-source platform for managing the end-to-end machine learning lifecycle.
    -   **MLflow Tracking:** Log parameters, metrics, and artifacts (including models) for each experiment run. Visualize and compare runs.
        -   **Example:** Logging hyperparameters like learning rate and regularization strength, metrics like accuracy and AUC, and the trained model itself to MLflow during each training run.
    -   **MLflow Projects:** Package your code and dependencies for reproducible runs. (Conceptual discussion for this class)
    -   **MLflow Models:** A standard format for packaging models that can be used with various deployment tools.
        -   **Example:** Saving a trained scikit-learn model in the MLflow Model format, which includes the model file, a description of the environment (e.g., `conda.yaml` or `requirements.txt`), and the model signature.
    -   **MLflow Registry:** (Optional) A centralized model store for managing the model lifecycle. (Conceptual discussion for this class)
    -   **Strengths:** Comprehensive, flexible, supports various ML frameworks, good for experiment tracking and model management, easy to use locally.
    -   **Weaknesses:** Some advanced features might require more complex setup.

-   **TensorBoard:**
    -   A visualization toolkit for TensorFlow.
    -   **Strengths:** Excellent for visualizing TensorFlow models.
    -   **Weaknesses:** Primarily focused on TensorFlow.

-   **Weights & Biases (W&B), Comet, Neptune.ai:**
    -   Commercial platforms for experiment tracking and collaboration.
    -   **Strengths:** User-friendly, powerful visualization, good for collaboration.
    -   **Weaknesses:** Commercial products, potential cost implications.

**For this class:** We will focus on **MLflow** as a practical and accessible tool for experiment tracking and model management. We will demonstrate how to use MLflow Tracking to log experiments and MLflow Models to package models.

### D. Model Deployment and Serving

For this class, we will focus on simple deployment options, avoiding Kubernetes.

-   **MLflow Models:** We will use MLflow's model packaging capabilities to create a standardized model artifact.
-   **Flask:** A lightweight Python web framework that can be used to create a simple REST API to serve predictions from the MLflow model.
    -   **Example:** Writing a Flask application that loads an MLflow model and exposes an endpoint that accepts input data and returns predictions.
-   **Other options (Conceptual Discussion):**
    -   **TensorFlow Serving:** Primarily for TensorFlow models.
    -   **TorchServe:** Model serving framework for PyTorch.
    -   **BentoML:** For packaging and deploying models as containers (can be used without Kubernetes for simple deployments).
    -   **Cloud-based deployment options (SageMaker, Azure ML, Vertex AI):** Briefly discuss these but not use them in practice.

**For this class:** We will demonstrate how to use MLflow in conjunction with Flask to create a simple REST API for serving model predictions, mirroring the simplified deployment approach discussed in the previous lesson. We'll also touch upon how this setup can be adapted for Continuous Delivery or Continuous Deployment.

### E. Model Monitoring and Management

We will focus on the conceptual aspects of monitoring and the basic tools for simple setups.

-   **Prometheus and Grafana:** Powerful open-source tools for monitoring and visualization. We will mention them as options but likely not use them in practice for this class.
-   **Cloud-based monitoring tools (CloudWatch, Azure Monitor, Google Cloud Monitoring):** These can be used to monitor cloud-based deployments.
-   **Simple custom dashboards:** We can use Python libraries like `matplotlib` or `streamlit` to create simple dashboards to visualize model performance metrics over time (logged in MLflow).

**For this class:** We will focus on the *importance* of monitoring and demonstrate how to log metrics with MLflow. We can also show how to create a simple dashboard to visualize these metrics, tying back to the monitoring principles discussed earlier.

### F. Data Versioning and Management

-   **DVC (Data Version Control), Pachyderm, Delta Lake:** Tools for managing and versioning data in ML projects.
    -   **Conceptual Role:** Tracking changes to datasets, ensuring reproducibility, and managing data pipelines.

**For this class:** We will briefly discuss the *importance* of data versioning but likely not implement these tools in practice. We can mention that these tools exist for managing data in a more robust MLOps pipeline.

### G. Choosing the Right Tools: A Pragmatic Approach

The best MLOps tools for your project depend on various factors. For this introductory class, we emphasize a pragmatic approach:

-   **Start simple:** Focus on core principles and readily available tools.
-   **MLflow is our primary tool:** We will use MLflow for experiment tracking, model packaging, and potentially simple model deployment.
-   **Python scripts for automation:** Illustrate automation principles without complex orchestration tools.
-   **Focus on core concepts:** Version control with Git, simple model serving with Flask, basic monitoring principles.

**Discussion Point:** How can we balance the need for simplicity with the desire to implement a robust MLOps pipeline? What are the trade-offs to consider when choosing tools, especially in light of the deployment and data pipeline considerations from the previous lesson?

