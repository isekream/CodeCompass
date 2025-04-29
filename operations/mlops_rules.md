# MLOps (Machine Learning Operations) Rules

## Core Principles

* **Automation:** Automate as much of the ML lifecycle as possible, including data ingestion, validation, preprocessing, feature engineering, model training, validation, deployment, and monitoring.
* **Reproducibility:** Ensure all steps in the ML process (data processing, training, evaluation, prediction) are reproducible. This requires versioning code, data, models, and configurations.
* **Collaboration:** Foster collaboration between Data Science, Engineering (ML, Data, Platform), and Operations teams. Use shared tools, platforms, and processes.
* **Continuous X (CI/CD/CT/CM):**
    * **Continuous Integration (CI):** Automate testing and validation of code (ML code, infrastructure code, pipeline definitions) and components (data validation, model validation).
    * **Continuous Delivery (CD):** Automate the deployment of ML training pipelines and prediction services.
    * **Continuous Training (CT):** Automate model retraining pipelines triggered by new data, code changes, performance degradation, or on a schedule.
    * **Continuous Monitoring (CM):** Continuously monitor data quality, model performance (online and offline metrics), model drift, and operational metrics of the ML system.
* **Versioning:** Version everything: datasets, code (feature engineering, model training, pipelines), models, configurations, and environments.

## Data Management

* **Data Validation:** Implement automated data validation steps in pipelines to check for schema adherence, statistical properties (distribution, missing values, outliers), and domain-specific rules. Fail pipelines or alert on significant data quality issues.
* **Data Versioning:** Use data versioning tools (e.g., DVC, LakeFS, Pachyderm) or strategies (e.g., immutable datasets in versioned storage paths) to track dataset changes and link them to specific model versions and experiments.
* **Feature Stores:** For larger teams or complex feature reuse scenarios, consider using a Feature Store to:
    * Centralize feature definitions and computation logic.
    * Ensure consistency between features used for training and serving (avoid training-serving skew).
    * Facilitate feature discovery and reuse across different models and teams.
    * Manage feature lineage and versioning.
    * Provide low-latency access for online serving (Online Store) and efficient access for training (Offline Store).
* **Data Lineage:** Track data lineage – where data comes from, how it's transformed, and where it's used – for debugging, auditing, and reproducibility.

## Experiment Tracking & Versioning

* **Track Experiments:** Use ML experiment tracking tools (e.g., MLflow Tracking, Weights & Biases, DVC Experiments, Vertex AI Experiments, SageMaker Experiments) to log:
    * Code version (Git commit hash).
    * Dataset version/identifier.
    * Hyperparameters.
    * Environment configuration (library versions).
    * Evaluation metrics.
    * Model artifacts (model files, plots).
* **Model Registry:** Use a Model Registry (e.g., MLflow Model Registry, cloud provider registries) to version, stage (staging, production), and manage the lifecycle of trained models. Store metadata, lineage information, and links to artifacts.

## Model Training & Validation

* **Automated Training Pipelines:** Define model training as automated, reproducible pipelines (e.g., using Kubeflow Pipelines, Airflow, Vertex AI Pipelines, SageMaker Pipelines, or custom scripts orchestrated via CI/CD).
* **Code Quality:** Apply general coding best practices (`python_data_science_rules.md`) to training code. Ensure code is modular, testable, and documented.
* **Model Validation:** Implement automated model validation within the training pipeline *before* registering or deploying a model. This should include:
    * Evaluation against a held-out test set using relevant metrics.
    * Comparison against baseline models or previous production versions.
    * Validation across important data segments (check for fairness and bias).
    * Potentially include checks for robustness or specific business logic constraints.
* **Resource Optimization:** Configure training jobs to use appropriate compute resources. Monitor resource utilization during training. Use distributed training frameworks if necessary for large models/datasets.

## Deployment

* **Automated Deployment:** Automate model deployment using CI/CD pipelines integrated with the Model Registry.
* **Deployment Strategies:** Choose appropriate deployment strategies based on risk tolerance and use case:
    * **Shadow Deployment:** Deploy the new model alongside the old one, sending production traffic to both but only using the old model's predictions. Compare predictions offline to evaluate the new model on live data without impacting users.
    * **Canary Release:** Gradually roll out the new model to a small subset of users/traffic. Monitor performance and errors closely before increasing traffic. Allows for safe rollout and easy rollback.
    * **A/B Testing / Multi-Armed Bandit:** Route traffic to multiple models simultaneously and compare performance based on business metrics to select the best performing model over time.
    * **Blue/Green Deployment:** Deploy the new model to a separate, identical environment. Switch traffic instantly once the new environment is validated. Provides fast rollback.
* **Prediction Service:** Deploy models as scalable prediction services (e.g., REST APIs using frameworks like FastAPI/Flask, containerized and served via Kubernetes, or using managed prediction services like SageMaker Endpoints, Vertex AI Endpoints, Azure ML Endpoints).
* **Versioning:** Ensure deployed prediction services clearly indicate the model version they are using.

## Monitoring (Continuous Monitoring - CM)

* **System Metrics:** Monitor operational metrics of the prediction service (latency, throughput/QPS, error rate, resource utilization - CPU/memory). Set up alerts for anomalies.
* **Data Drift:** Monitor the statistical distribution of incoming prediction data and compare it to the training data distribution. Detect significant drift that might invalidate the model's assumptions.
* **Concept Drift / Model Performance:** Monitor the model's predictive performance on live data.
    * If ground truth is available quickly, track metrics like accuracy, precision, recall, F1, MAE, MSE directly.
    * If ground truth is delayed or unavailable, use proxy metrics (e.g., prediction distribution stability, data drift correlation with performance, business KPIs) to infer potential degradation.
* **Feedback Loops:** Establish mechanisms to collect ground truth labels and user feedback to evaluate real-world performance and trigger retraining.
* **Alerting:** Set up automated alerts for significant model performance degradation, data drift, or operational issues.

## Infrastructure & Automation

* **Infrastructure as Code (IaC):** Manage all supporting infrastructure (compute for training/serving, databases, storage, message queues, feature stores) using IaC (Terraform, etc.).
* **Containerization:** Use containers (Docker) to package ML code, dependencies, and configurations for consistent execution across environments (dev, test, prod, training, serving).
* **Orchestration:** Use workflow orchestration tools (Airflow, Kubeflow Pipelines, Argo Workflows, etc.) to manage complex ML pipelines involving multiple steps and dependencies.
* **CI/CD Integration:** Integrate all MLOps steps (data validation, training, testing, deployment, monitoring checks) into CI/CD pipelines.

## Collaboration & Governance

* **Cross-Functional Teams:** Foster collaboration between data scientists, ML engineers, data engineers, DevOps/SRE, and business stakeholders throughout the ML lifecycle.
* **Documentation:** Document data sources, features, experiments, models, pipeline definitions, and deployment processes.
* **Naming Conventions:** Establish clear naming conventions for experiments, datasets, features, models, and endpoints.
* **Access Control:** Implement appropriate access controls (IAM) for data, code repositories, feature stores, model registries, and deployment environments based on the principle of least privilege.
* **Compliance & Ethics:** Ensure ML systems comply with relevant regulations (e.g., GDPR, CCPA) and ethical AI principles (fairness, transparency, accountability). Monitor models for bias.
