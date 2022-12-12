# Integrating BigQuery with Airflow

## Step 1: Initial Setup

### Step 1.1: Create an Airflow directory when starting a new project

### Step 1.2: Import the latest version of Airflow

```bash
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/stable/docker-compose.yaml'
```
### Step 1.3: Set up Airflow User

Ensure the user and group permissions are the same between the host folders and the folders in our container using the following code:
  

If starting with a blank setup:
 ```bash
 mkdir -p ./dags ./logs ./plugins
 ```
 
 ```bash
 echo -e "AIRFLOW_UID=$(id-u)">.env
 ```
 * Host user ID must be set to 0

### Step 1.4: Creating the Docker File
When running Airflow locally, we may want to use an extended image using additional dependencies.

a. Create a Docker file pointing to the Docker-compose file which we downloaded in Step 1.2 
  a.1. Set the FROM statement to apache/airflow:version_number
* Add the custom package gcloud
* In the Docker-compose file change the image from the Airflow version to the Docker File we just created






















*** Chapter 3.3
