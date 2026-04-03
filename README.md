# Sephora Recommender Pipeline

This project builds a Bronze -> Silver -> Gold data pipeline in Databricks for a Sephora recommender system.

It includes:
- Databricks notebooks for Bronze, Silver, and Gold layers
- a Databricks Asset Bundle job definition
- setup steps so teammates or instructors can recreate the pipeline in their own workspace

---

## What this project does

### Bronze layer
Loads raw Sephora CSV files into Delta tables with minimal transformation.

### Silver layer
Cleans and standardizes the Bronze data:
- standardizes column names
- fixes data types
- cleans review fields
- parses ingredient fields
- creates reusable keys for later joins

### Gold layer
Creates final tables for recommender system experiments:
- product features
- review features
- user-product interactions

These Gold tables support:
- popularity-based recommenders
- content-based recommenders
- collaborative filtering
- hybrid recommender experiments

---

## Project structure

```text
sephora-recommender/
  data_pipeline/
    bronze_layer.ipynb
    silver_layer.ipynb
    gold_layer.ipynb
  resources/
    sephora_bronze_silver_gold_pipeline.job.yml
  databricks.yml
  README.md
```

---

## Raw data needed

Upload these files to Databricks before running the pipeline:

- `product_info.csv`
- `cosmetic_p.csv`
- `reviews_0-250.csv`
- `reviews_250-500.csv`
- `reviews_500-750.csv`
- `reviews_750-1250.csv`
- `reviews_1250-end.csv`

---

## Expected Databricks file path

The notebooks currently expect the raw files to be stored here:

```python
/Volumes/dbacademy/ingesting_data/myfiles/sephora_raw
```

If your Databricks workspace uses a different volume path, update the notebook config.

---

## Environment assumptions

The notebooks currently use:

- **catalog:** `dbacademy`
- **schema:** `ingesting_data`

If your workspace uses a different catalog or schema, change the config at the top of the notebooks.

---

## How to set it up

### 1. Clone the repository

```bash
git clone https://github.com/Awearse/sephora-recommender.git
cd sephora-recommender
```

### 2. Upload raw data to Databricks

Upload all required CSV files into:

```python
/Volumes/dbacademy/ingesting_data/myfiles/sephora_raw
```

### 3. Install Databricks CLI

On Mac:

```bash
brew tap databricks/tap
brew install databricks
databricks -v
```

### 4. Log in to Databricks CLI

Find your workspace host from the Databricks browser URL.

Example:

```text
https://dbc-xxxxxxxx.cloud.databricks.com
```

Then log in:

```bash
databricks auth login --host https://<your-databricks-workspace-host>
```

### 5. Validate the Databricks bundle

From the repo root:

```bash
databricks bundle validate
```

### 6. Deploy the job

```bash
databricks bundle deploy -t dev
```

This creates or updates the Databricks pipeline job in your workspace.

### 7. Run the pipeline

```bash
databricks bundle run sephora_bronze_silver_gold_pipeline -t dev
```

This runs the notebooks in order:

```text
bronze_layer -> silver_layer -> gold_layer
```

---

## Output tables

The pipeline creates Bronze, Silver, and Gold Delta tables.

### Bronze
- `bronze_sephora_product_info`
- `bronze_skincare_cosmetic_p`
- `bronze_sephora_reviews`

### Silver
- `silver_sephora_product_info`
- `silver_skincare_cosmetic_p`
- `silver_sephora_reviews`
- `silver_ingredients_exploded`

### Gold
- `gold_product_features`
- `gold_product_review_features`
- `gold_user_product_interactions`

---

## How to check if it worked

Run these in Databricks SQL or a notebook:

```sql
SHOW TABLES IN dbacademy.ingesting_data;
```

```sql
SELECT COUNT(*) FROM dbacademy.ingesting_data.gold_product_features;
```

```sql
SELECT COUNT(*) FROM dbacademy.ingesting_data.gold_user_product_interactions;
```

---

## Notes

- This pipeline currently runs as a **full refresh**.
- Each run rebuilds Bronze, Silver, and Gold tables from the raw files.
- The bundle recreates the Databricks job config, but not the raw data files.
- Raw data must be uploaded before running the job.

---

## Troubleshooting

### Bundle validation fails
Make sure you are running commands from the repo root and that `databricks.yml` exists there.

### Job deploy works but pipeline run fails
Check:
- the raw files exist in the expected Databricks volume path
- the catalog and schema exist
- you have permission to create tables and jobs

### Gold tables do not appear
Check the Bronze and Silver task logs in the Databricks job run.

---

## Main Databricks job resource

```text
sephora_bronze_silver_gold_pipeline
```
