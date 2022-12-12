# Integrating BigQuery with Airflow

## Step 1: Initial Setup

### Step 1.1: Create an Airflow directory when starting a new project

### Step 1.2: Import the latest version of Airflow

```bash
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/stable/docker-compose.yaml'
```
### Step 1.3: Set up Airflow User

Create dags, logs, and plugins folders and set AIRFLOW UID in the .env file:
  
 ```bash
 mkdir -p ./dags ./logs ./plugins
 echo -e "AIRFLOW_UID=$(id-u)">.env
 ```
 * Ensure the user and group permissions are the same between the host folders and the folders in our container using the following code
 * Host user ID must be set to 0

### Step 1.4: Creating the Docker File

When running Airflow locally, we may want to use an extended image using additional dependencies.
 
* Create a docker file pointing to the docker-compose file downloaded in step 1.2 and add the custom package gcloud
  * Use the format referenced [here](https://airflow.apache.org/docs/docker-stack/recipies.html) to create the docker file used to download Google Cloud SDK
  * Set the FROM statement to `FROM apache/airflow:version.number`
* In the docker-compose file change the image from the Airflow version to the docker file we just created
* Create a requirements.txt file that contains all of the libraries we want to install
* set `AIRFLOW__CORE__LOAD__EXAMPLES: 'false'` in the docker-compose file
* Add Google Cloud information to the environment section of the docker-compose file: 
```yaml
GOOGLE_APPLICATION_CREDENTIALS: /.google/credentials/google_credentials.json
AIRFLOW_CONN_GOOGLE_CLOUD_DEFAULT: 'google-cloud-platform://?extra__google_cloud_platform__key_path=/.google/credentials/google_credentials.json'
GCP_PROJECT_ID: 'pivotal-surfer-336713'
GCP_GCS_BUCKET: 'dtc_data_lake_pivotal-surfer-336713'
```

## Step 2: Building the Environment

### Step 2.1: Build the Docker Image

```bash
docker-compose build
```
Any time there is a change to our Docker file, this code needs to be run again to update the containers

### Step 2.2: Initialize

```bash
docker-compose up airflow-init
docker-compose up
```

Once the code is done running, the status of the containers can be checked by running the following code in a new terminal

```bash
docker ps
```

### Step 2.3: Access the Airflow GUI

Using the link specified on the airflow-webserver container, enter the Airflow GUI and sign in


















*** Chapter 3.3
