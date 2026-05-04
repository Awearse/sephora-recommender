# Sephora Recommender System with Databricks Medallion Pipeline

This repository contains the source code, Databricks notebooks, data pipeline configuration, and model development notebooks for a Sephora product recommender system developed as a final Big Data Analytics project.

The project uses a Databricks-based Bronze, Silver, and Gold medallion architecture to process Sephora product, review, ingredient, and external trend data before training recommender models. The final system supports:

- Popularity-based recommendation
- Matrix Factorisation recommendation
- Hybrid recommendation using item-based collaborative filtering and content-based filtering

The purpose of this repository is to allow another user, especially professors and project evaluators, to reproduce the same data pipeline and model results in Databricks.

---

## Project Summary

This project builds a batch recommender system pipeline for Sephora beauty and skincare products.

The pipeline follows this full-refresh flow:

```text
Raw CSV files
→ Bronze layer
→ Bronze health check
→ Silver layer
→ Silver health check
→ Gold layer
→ Gold health check
→ Hybrid recommender model
→ Matrix Factorisation model
→ Popularity recommender model
```

The project uses static CSV datasets. Therefore, the pipeline is designed as a batch processing workflow rather than a real-time streaming pipeline.

---

## Repository Structure

```text
sephora-recommender/
│
├── data_pipeline/
│   ├── bronze_layer.ipynb
│   ├── silver_layer.ipynb
│   ├── gold_layer.ipynb
│   └── pipeline_health_checker.ipynb
│
├── data_pipeline_config/
│   └── Sephora_full_refresh_pipeline_with_monitoring.yml
│
├── data_sources/
│   ├── cosmetic_p.csv
│   └── popular_beauty_products.csv
│
├── eda/
│   └── exploratory analysis notebooks
│
├── models/
│   ├── FINAL Hybrid_Train_Test_Split_0.7IBCF_0.3ContentF.ipynb
│   ├── MF Train Test Split (Reduce Data Sparsity).ipynb
│   └── Popularity Recommender.ipynb
│
└── README.md
```

Note: The exact notebook paths in Databricks must match the paths written in the YAML file inside `data_pipeline_config/`. If the notebooks are renamed or moved, update the YAML file before running the workflow.

---

## Data Sources

This project uses three main types of data.

### 1. Primary Sephora Product and Review Dataset

The main product and review dataset is too large to store in this GitHub repository.

Please download it manually from Kaggle:

```text
https://www.kaggle.com/datasets/nadyinky/sephora-products-and-skincare-reviews
```

Required files from the Kaggle dataset:

```text
product_info.csv
reviews_0-250.csv
reviews_250-500.csv
reviews_500-750.csv
reviews_750-1250.csv
reviews_1250-end.csv
```

These files are required for the Bronze layer to run successfully.

### 2. Ingredient Dataset

The ingredient dataset is included in this repository:

```text
data_sources/cosmetic_p.csv
```

This file is used to enrich product-level features with ingredient information.

### 3. Google Trends / Market Signal Dataset

The Google Trends or market signal dataset is included in this repository:

```text
data_sources/popular_beauty_products.csv
```

This file provides additional external trend information for the project.

---

## Why the Kaggle Dataset Is Not Included

The Kaggle Sephora review dataset contains large CSV files that are not suitable for normal GitHub storage.

For reproducibility, users should download the Kaggle files separately and upload them into Databricks together with the smaller CSV files included in this repository.

---

## Technologies Used

This project uses:

```text
Databricks
PySpark
Spark SQL
Delta Lake
Databricks Workflows
Databricks Volumes
MLflow
GitHub
Kaggle dataset
Google Trends data
```

---

## System Requirements

To reproduce this project, you need:

```text
1. A Databricks workspace
2. Permission to create schemas, tables, volumes, and jobs
3. Access to Databricks Repos or Workspace notebooks
4. A Databricks serverless or cluster compute environment that supports PySpark
5. The raw CSV files uploaded into the expected Databricks volume path
6. The YAML job configuration file from data_pipeline_config/
```

---

## Expected Databricks Setup

The notebooks are designed to run in Databricks.

The expected catalog and schema are:

```python
catalog_name = "workspace"
schema_name = "default"
```

The expected Databricks volume path for raw data is:

```text
/Volumes/workspace/default/sephora_surfers/
```

All required CSV files should be uploaded directly into this folder.

Do not place the CSV files inside an additional nested folder unless you also update the file paths in the notebooks.

---

## Required Raw Files in Databricks

After downloading the Kaggle dataset and cloning this repository, upload the following files into Databricks:

```text
/Volumes/workspace/default/sephora_surfers/product_info.csv
/Volumes/workspace/default/sephora_surfers/reviews_0-250.csv
/Volumes/workspace/default/sephora_surfers/reviews_250-500.csv
/Volumes/workspace/default/sephora_surfers/reviews_500-750.csv
/Volumes/workspace/default/sephora_surfers/reviews_750-1250.csv
/Volumes/workspace/default/sephora_surfers/reviews_1250-end.csv
/Volumes/workspace/default/sephora_surfers/cosmetic_p.csv
/Volumes/workspace/default/sephora_surfers/popular_beauty_products.csv
```

The following files come from Kaggle:

```text
product_info.csv
reviews_0-250.csv
reviews_250-500.csv
reviews_500-750.csv
reviews_750-1250.csv
reviews_1250-end.csv
```

The following files come from this repository:

```text
data_sources/cosmetic_p.csv
data_sources/popular_beauty_products.csv
```

---

## How to Clone the Repository

Run the following commands:

```bash
git clone https://github.com/Awearse/sephora-recommender.git
cd sephora-recommender
```

---

## How to Import the Repository into Databricks

In Databricks:

1. Go to **Workspace**
2. Open **Repos**
3. Click **Add Repo**
4. Paste this GitHub URL:

```text
https://github.com/Awearse/sephora-recommender
```

5. Clone the repository into your Databricks workspace.

The YAML job configuration assumes that the repository is located at a path similar to:

```text
/Workspace/Users/<your_databricks_email>/sephora-recommender/
```

If your repository is cloned into a different Databricks path, update the notebook paths in the YAML file before running the job.

---

## Databricks Job YAML Configuration

The Databricks workflow configuration is stored in:

```text
data_pipeline_config/Sephora_full_refresh_pipeline_with_monitoring.yml
```

This YAML file defines the full pipeline workflow.

Before running it, update the placeholders:

```text
<your_databricks_email>
your_email@example.com
teammate_email@example.com
```

For example, change this:

```yaml
notebook_path: /Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/bronze_layer
```

to your actual Databricks path:

```yaml
notebook_path: /Workspace/Users/name@example.com/sephora-recommender/data_pipeline/bronze_layer
```

Also update the email notification section:

```yaml
email_notifications:
  on_failure:
    - your_email@example.com
    - teammate_email@example.com
```

These emails are used by Databricks to notify users if a pipeline task fails.

---

## Important Notebook Path Check

Before running the workflow, confirm that all notebook paths in the YAML file exist in your Databricks workspace.

Expected notebook paths:

```text
/Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/bronze_layer
/Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/pipeline_health_checker
/Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/silver_layer
/Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/gold_layer
/Workspace/Users/<your_databricks_email>/sephora-recommender/models/FINAL Hybrid_Train_Test_Split_0.7IBCF_0.3ContentF
/Workspace/Users/<your_databricks_email>/sephora-recommender/models/MF Train Test Split (Reduce Data Sparsity)
/Workspace/Users/<your_databricks_email>/sephora-recommender/models/Popularity Recommender
```

If the actual notebook names are different, update the YAML file to match the exact Databricks notebook paths.

Databricks notebook paths usually do not include the `.ipynb` extension inside the workflow YAML.

---

## How to Create the Databricks Workflow from YAML

In Databricks:

1. Open the file:

```text
data_pipeline_config/Sephora_full_refresh_pipeline_with_monitoring.yml
```

2. Copy the full YAML content.

3. Go to **Workflows**.

4. Create a new job using YAML or switch to YAML view if available.

5. Paste the YAML content.

6. Confirm that all notebook paths are correct.

7. Save the job.

8. Click **Run now**.

---

## Pipeline Execution Order

The workflow runs in the following order.

### 1. Bronze Layer

Notebook:

```text
data_pipeline/bronze_layer
```

Purpose:

```text
Loads raw CSV files into Bronze Delta tables with minimal transformation.
Standardizes column names.
Adds ingestion metadata.
Preserves the raw source data structure as much as possible.
```

Expected Bronze tables:

```text
workspace.default.bronze_sephora_product_info
workspace.default.bronze_skincare_cosmetic_p
workspace.default.bronze_sephora_reviews
```

---

### 2. Bronze Health Check

Notebook:

```text
data_pipeline/pipeline_health_checker
```

Parameter:

```text
layer = bronze
```

Purpose:

```text
Checks that the Bronze tables exist and contain data before the Silver layer runs.
```

---

### 3. Silver Layer

Notebook:

```text
data_pipeline/silver_layer
```

Purpose:

```text
Cleans and standardizes the Bronze data.
Fixes data types.
Handles missing values.
Cleans review and product fields.
Parses ingredient fields.
Creates normalized keys for joins and downstream modelling.
```

Expected Silver tables:

```text
workspace.default.silver_sephora_product_info
workspace.default.silver_skincare_cosmetic_p
workspace.default.silver_sephora_reviews
workspace.default.silver_ingredients_exploded
```

---

### 4. Silver Health Check

Notebook:

```text
data_pipeline/pipeline_health_checker
```

Parameter:

```text
layer = silver
```

Purpose:

```text
Checks that the Silver tables exist and contain data before the Gold layer runs.
```

---

### 5. Gold Layer

Notebook:

```text
data_pipeline/gold_layer
```

Purpose:

```text
Creates final modelling-ready tables for recommender systems.
Builds product-level features.
Builds review-level features.
Builds user-product interaction data.
Prepares Gold datasets for model training and evaluation.
```

Expected Gold tables:

```text
workspace.default.gold_product_features
workspace.default.gold_product_review_features
workspace.default.gold_user_product_interactions
```

---

### 6. Gold Health Check

Notebook:

```text
data_pipeline/pipeline_health_checker
```

Parameter:

```text
layer = gold
```

Purpose:

```text
Checks that the Gold tables exist and contain data before model training begins.
```

---

### 7. Hybrid Recommender Model

Notebook:

```text
models/FINAL Hybrid_Train_Test_Split_0.7IBCF_0.3ContentF
```

Purpose:

```text
Builds a hybrid recommender system using item-based collaborative filtering and content-based filtering.

The hybrid score combines:
- 0.7 item-based collaborative filtering score
- 0.3 content-based filtering score
```

This model uses product interaction signals and product content features to generate recommendations.

---

### 8. Matrix Factorisation Model

Notebook:

```text
models/MF Train Test Split (Reduce Data Sparsity)
```

Purpose:

```text
Trains a Matrix Factorisation recommender model using user-product interaction data from the Gold layer.

This model is used for collaborative filtering and recommendation ranking.
```

---

### 9. Popularity Recommender Model

Notebook:

```text
models/Popularity Recommender
```

Purpose:

```text
Builds a baseline popularity recommender using product-level signals such as rating count, average rating, and product engagement.
```

This model is useful for cold-start users where personalized interaction history may not be available.

---

## How to Verify That the Pipeline Ran Successfully

After the workflow completes, run the following SQL command in Databricks SQL or a Databricks notebook:

```sql
SHOW TABLES IN workspace.default;
```

Check the Bronze tables:

```sql
SELECT COUNT(*) FROM workspace.default.bronze_sephora_product_info;
SELECT COUNT(*) FROM workspace.default.bronze_skincare_cosmetic_p;
SELECT COUNT(*) FROM workspace.default.bronze_sephora_reviews;
```

Check the Silver tables:

```sql
SELECT COUNT(*) FROM workspace.default.silver_sephora_product_info;
SELECT COUNT(*) FROM workspace.default.silver_skincare_cosmetic_p;
SELECT COUNT(*) FROM workspace.default.silver_sephora_reviews;
SELECT COUNT(*) FROM workspace.default.silver_ingredients_exploded;
```

Check the Gold tables:

```sql
SELECT COUNT(*) FROM workspace.default.gold_product_features;
SELECT COUNT(*) FROM workspace.default.gold_product_review_features;
SELECT COUNT(*) FROM workspace.default.gold_user_product_interactions;
```

A successful run should produce non-zero row counts for the main product, review, interaction, and feature tables.

---

## Expected Outputs

The project produces:

```text
1. Bronze Delta tables containing raw ingested data
2. Silver Delta tables containing cleaned and standardized data
3. Gold Delta tables containing modelling-ready recommender features
4. Popularity-based recommendations
5. Matrix Factorisation recommendations
6. Hybrid recommender results
7. Model evaluation metrics
8. Pipeline health check results
```

---

## Full Refresh Design

This project uses a full-refresh pipeline.

Each run rebuilds the Bronze, Silver, and Gold tables from the raw CSV files.

This design was selected because the project uses static CSV datasets and does not connect to a live production data source.

This means:

```text
1. The raw CSV files must be available before each run.
2. Existing Delta tables may be overwritten.
3. The same input files should reproduce the same output tables.
4. Incremental ingestion is not required for this MVP version.
```

---

## Why the Model Tasks Run Sequentially

The model notebooks are executed sequentially after the Gold layer passes validation.

Although the Hybrid, Matrix Factorisation, and Popularity models could theoretically run in parallel from the same Gold tables, a sequential model execution flow was used to reduce compute pressure in the Databricks serverless environment and improve pipeline stability.

The model execution order is:

```text
Hybrid model
→ Matrix Factorisation model
→ Popularity model
```

---

## Reproducibility Checklist

Before running the Databricks workflow, confirm the following:

```text
[ ] The repository has been cloned into Databricks Repos.
[ ] The Kaggle Sephora dataset has been downloaded.
[ ] product_info.csv has been uploaded to /Volumes/workspace/default/sephora_surfers/.
[ ] All reviews_*.csv files have been uploaded to /Volumes/workspace/default/sephora_surfers/.
[ ] cosmetic_p.csv has been uploaded to /Volumes/workspace/default/sephora_surfers/.
[ ] popular_beauty_products.csv has been uploaded to /Volumes/workspace/default/sephora_surfers/.
[ ] The Databricks catalog is workspace.
[ ] The Databricks schema is default.
[ ] The volume path /Volumes/workspace/default/sephora_surfers/ exists.
[ ] The pipeline_health_checker notebook exists in data_pipeline/.
[ ] The model notebook paths in the YAML match the actual Databricks paths.
[ ] The placeholder emails in the YAML have been replaced.
[ ] The placeholder <your_databricks_email> has been replaced.
[ ] The Databricks user has permission to create schemas, tables, volumes, and jobs.
```

---

## Troubleshooting

### 1. Bronze layer fails because files cannot be found

Check that the files are uploaded directly into:

```text
/Volumes/workspace/default/sephora_surfers/
```

Also check that the file names match the expected patterns:

```text
product_info*.csv
cosmetic_p*.csv
reviews_*.csv
popular_beauty_products*.csv
```

---

### 2. YAML job fails because a notebook path does not exist

Check the exact notebook location in Databricks.

For example, this path:

```text
/Workspace/Users/<your_databricks_email>/sephora-recommender/data_pipeline/bronze_layer
```

must match the actual path in your workspace.

If the notebook is inside another folder, update the YAML file.

---

### 3. Health check task fails

Confirm that this notebook exists:

```text
data_pipeline/pipeline_health_checker.ipynb
```

If the notebook is missing, either add it to the repository or remove the health check tasks from the YAML file.

---

### 4. Model task fails

Check whether the model notebooks are stored exactly where the YAML expects them to be.

If the YAML path is:

```text
models/FINAL Hybrid_Train_Test_Split_0.7IBCF_0.3ContentF
```

then the notebook must exist at that same location in Databricks.

If the notebook has been renamed or moved, update the YAML file.

---

### 5. Permission error when creating schema or tables

The Databricks user must have permission to create or overwrite tables in:

```text
workspace.default
```

If permission is denied, request schema-level privileges from the Databricks workspace administrator.

---

### 6. Model runs out of memory or exceeds serverless limits

The Hybrid model may be compute-intensive because it uses text-based features and similarity calculations.

Possible fixes:

```text
1. Reduce the number of text features.
2. Reduce the sampled training dataset size.
3. Run the model on a larger Databricks cluster.
4. Avoid running multiple model notebooks in parallel.
```

---

## Notes for Professors and Evaluators

This repository is designed to support reproducibility of the final project results.

However, the large Kaggle Sephora product and review dataset is not included in GitHub due to file size limitations.

To reproduce the project:

```text
1. Clone this repository into Databricks.
2. Download the Kaggle Sephora dataset.
3. Upload the required CSV files into the Databricks volume.
4. Upload the included data_sources CSV files into the same volume.
5. Update the YAML placeholders.
6. Create the Databricks workflow using the YAML file.
7. Run the workflow.
8. Verify the output Delta tables and model results.
```

The project is implemented as a Databricks workflow rather than a local Python script because the project focuses on big data engineering, medallion architecture, distributed data processing, and recommender system development using Spark.

---

## Project Limitations

This project was developed as an academic MVP.

Main limitations:

```text
1. The pipeline relies on static CSV files instead of live production data.
2. The system uses batch processing rather than real-time ingestion.
3. The recommender models are evaluated mainly through offline metrics.
4. Business impact metrics such as click-through rate, conversion rate, conversion uplift, or customer satisfaction are not measured directly.
5. The Hybrid model was simplified to fit within Databricks serverless compute constraints.
```

Future improvements could include:

```text
1. Incremental data ingestion
2. Automated model retraining
3. Real-time recommendation serving
4. Online A/B testing
5. Production model monitoring
6. Model drift and data drift detection
7. Recommendation API deployment
```

---

## Contributors

This project was completed as part of a final Big Data Analytics project.

Repository owner:

```text
Awearse
```

---

## License and Data Usage

This repository is intended for academic and educational use.

The primary Sephora product and review dataset belongs to its original Kaggle source. Users should follow the dataset terms of use on Kaggle when downloading or reusing the data.

The large Kaggle CSV files are intentionally excluded from this repository.
