# 🛡️ AI-Powered Anti-Money Laundering (AML) Pipeline on Databricks Lakehouse

![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=Databricks&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=Apache%20Spark&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?style=for-the-badge&logo=MLflow&logoColor=white)
![Machine Learning](https://img.shields.io/badge/Machine%20Learning-Random%20Forest-4CAF50?style=for-the-badge)

## 📌 Business Overview
Financial institutions lose billions annually to money laundering schemes. Traditional rule-based systems generate high false-positive rates, exhausting investigation teams. 

This project implements an end-to-end **Machine Learning pipeline** to detect complex Anti-Money Laundering (AML) patterns—specifically **Account Takeovers** and **Smurfing**—using the highly scalable Databricks Lakehouse Platform.

## 🏗️ Architecture: The Medallion Approach
This project strictly adheres to the **Medallion Data Architecture** to guarantee ACID compliance, data quality, and scalable ML inference.

* **🥉 Bronze Layer (`01_Bronze_Data_Ingestion`)**: Ingested raw synthetic financial records (PaySim) from secure Unity Catalog Volumes into optimized Delta Tables.
* **🥈 Silver Layer (`02_Silver_Feature_Engineering`)**: Cleansed data and performed heavy-duty feature engineering using PySpark Window functions to extract behavioral patterns.
* **🤖 MLOps Layer (`03_Machine_Learning_MLflow`)**: Trained a Random Forest Classifier. Handled hyperparameter tracking, custom model signatures, and Unity Catalog Model Registry via **MLflow**.
* **🥇 Gold Layer (`04_Gold_Business_Analytics`)**: Applied batch inference to generate a refined Delta Table highlighting top high-risk transactions for the banking investigation team.
* **⚙️ Orchestration**: The entire workflow is automated sequentially using **Databricks Workflows (Jobs)**.

## 🧠 Advanced Feature Engineering & ML Strategy
Building a model on synthetic data requires deep domain understanding to avoid overfitting to the simulator's logic.

1. **Combating Data Bias (The 'Amount' Drop):** The PaySim simulator hard-coded a $10M cap for major frauds. A naive model would overfit this rule. I intentionally **dropped the `amount` feature** during V2 training, forcing the model to learn actual fraudulent behaviors rather than exploiting the simulator's cheat code.
2. **System Manipulation Detection (`Origin_Balance_Anomaly`):** Calculated the mathematical mismatch between `oldbalance`, `newbalance`, and `amount`. Fraudsters draining accounts often bypass standard ledger math, which this feature perfectly captures.
3. **Velocity Tracking for Smurfing (`Velocity_Score`):** Leveraged PySpark `Window.partitionBy()` to calculate transaction frequencies per account over time, exposing rapid hit-and-run behaviors.

## 🛠️ Challenges Conquered
During the MLOps implementation, strict enterprise governance rules were enforced:
* **Unity Catalog Security:** Configured explicit staging directories (`MLFLOW_DFS_TMP`) to allow MLflow to save large SparkML artifacts securely within UC Volumes.
* **Spark Connect vs. MLflow Signatures:** Resolved GRPC pipeline crashes by selectively dropping complex MLlib Vector columns (`features`, `probability`, `rawPrediction`) before inferring strict I/O model signatures for the Unity Catalog Registry.

## 🚀 How to Run the Pipeline
1. Clone this repository into your Databricks Workspace.
2. Download the `PaySim1` dataset from Kaggle and upload it to your Unity Catalog Volume.
3. Update the `raw_csv_path` and `catalog` names in the notebooks to match your workspace setup.
4. Run the notebooks sequentially (01 -> 02 -> 03 -> 04) or trigger the predefined **Databricks Job**.

## 📊 Results & Business Impact
* Successfully trained a highly interpretable **Random Forest** model with an AUC-ROC of >0.99.
* Deployed an automated Gold pipeline that filters out noise and delivers a concise dashboard of actionable, high-probability fraud events for immediate account freezing.

---
*Created by [Karan] as part of an advanced Data Engineering and AI Capstone Project.*
