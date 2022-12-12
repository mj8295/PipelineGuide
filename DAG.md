# DAGs

## Creating a DAG in Python

### Step 1: Import packages

At the very least the following packages are needed:

```python
import os
import logging

from airflow import DAG
from datetime import datetime
from airflow.operators.bash import BashOperator
from airflow.operators.python import PythonOperator

from google.cloud import storage
from airflow.providers.google.cloud.operators.bigquery import BigQueryCreateExternalTableOperator

import pyarrow.csv as pv
import pyarrow.parquet as pq
```

### Step 2: Begin Construction of the DAG

Airflow home will need to be defined as a variable we can reference later on:

```python
local_home = os.environ.get("AIRFLOW_HOME", "/opt/airflow/")
```

The beginning of any DAG is as follows:

```python
local_workflow = DAG(
  "DAG_NAME"
  )
```

### Step 3: Define the Upload_to_GCS function:

If uploading data to Google Cloud a function needs to be made to reference later:

```python
def upload_to_gcs(bucket, object_name, local_file):
  storage.blob._MAX_MULTIPART_SIZE = 5 * 1024 * 1024
  storage.blob._DEFAULT_CHUNKSIZE = 5 * 1024 * 1024
  
  client = storage.Client()
  bucket = client.bucket(bucket)
  
  blob = bucket.blob(object_name)
  blob.upload_from_filename(local_file)
  ```
  
### Step 4: Assign default arguments
  
These are not necessary but may help the readability of your code.  Some commpon arguments are:

```python
default_args = {
  "Owner":"airflow"
  "start_date": days_ago(1)
  "depends_on_past": False,
  "retries": 1,
  }
```

### Step 5.1: Begin writing out the DAG

The first part of our dag will we the WITH statement referenced in step 2.  Now we will add a couple more arguments to it to help it run more smoothly

```python
with DAG(
    dag_id="data_ingestion_gcs_dag",
    schedule_interval="@daily",
    default_args=default_args,
    catchup=False,
    max_active_runs=1,
    tags=['dtc-de'],
) as dag:
```

### Step 5.1: Add our DAG tasks

To demonstrate the BashOperator, PythonOperator, and BigQueryCreateExternalTableOperator,  we will create a DAG that downloads data from the web using bash and then ingests the data using python.

Download data:

```python
download_dataset_task = BashOperator(
    task_id="download_dataset_task",
    bash_command=f"curl -sSL {dataset_url} > {path_to_local_home}/{dataset_file}"
)
```

Ingest data:
```python
format_to_parquet_task = PythonOperator(
    task_id="format_to_parquet_task",
    python_callable=format_to_parquet,
    op_kwargs={
        "src_file": f"{path_to_local_home}/{dataset_file}",
    },
)
```
```python
bigquery_external_table_task = BigQueryCreateExternalTableOperator(
    task_id="bigquery_external_table_task",
    table_resource={
        "tableReference": {
            "projectId": PROJECT_ID,
            "datasetId": BIGQUERY_DATASET,
            "tableId": "external_table",
        },
        "externalDataConfiguration": {
            "sourceFormat": "PARQUET",
            "sourceUris": [f"gs://{BUCKET}/raw/{parquet_file}"],
        },
    },
)
```
Note thate we used several variables here as well as a python function we defined.  This increases the readability of the code but we need to make sure that each variable is defined.  All the variables and functions here should be defined outside the DAG.

To set the order each Dag is run we need to define it using the following code:

```python
task_name_1 >> task_name_2 >> task_name_3
```

  
  
  
