# Google Cloud Setup

## 1.1 - Create a New Project

On the Google Cloud wecome page select the project dropdown menu.
Select the "New Project" button in the top right corner of the window that pops up.

## 1.2 - Create a Service Account

In the Management section of the Google Cloud Console select the IAM & Admin service
Select the Service Accounts tab on the left side of the page
Select Create Service Account

## 1.3 - Generate a Key

In the Service Accounts Tab, click on actions and select:
    
    Manage Keys -> Add Key -> Create New Key -> Save Private Key

## 1.4 - Refresh Token & Verify Authentication

In order to easily connect to our project in the future we need teo run the following code:
    
    gcloud auth application-default login

## 1.5 - Enable API Access

 

## 1.6 - Generate ssh Keys

We need to create ssh keys to be used to connect to the cloud.  To do this we run the following code in the command prompt:
    
    ssk-keygen -t rsa -f ~/.ssh/<key_file_name> - <max> -b 2048

## 1.7 - Link Public Key to Cloud

In the compute engine sectione of the Google Cloud Console select:

    Metadata -> ssh keys -> Add ssh Key
    
After these steos are followed you should be able to enter the public key.  To obtain the public key run the following code in the command prompt:

    cat KEY.pub
    
  Save file to .gc folder

## 1.8 - Create a new Virtual Machine Instance

In the compute engine section of the Google Cloud console select:

    VM Instances -> Create Instance
    
Select a region, computation power, and operating system

## 1.9 - Configuration

### 1.9.1 - Connection Configuration

Create a config file in the .ssh folder

    touch config

Open the file in Visual Studio

    code config

Enter the configuration information

    Host <Project Name>
		  HostName <External IP of VM>
		  UserName <Name used to generate ssh key>
		  IdentityFile <location of key>

Following these steps enable the connection to the VM using the following codein the command prompt:
	
  ssh <project-name>

### 1.9.2 - Visual Studio Configuration

Ensure Remote SSH extension is installed in VS
In the bottom left of VS click “Open Remote Window”
Click on “Connect to Host” and select the project
If we made the config file first here the project should appear

### 1.9.3 - Google Cloud CLI Configuration

Create “GOOGLE_APPLICATION_CREDENTIALS” environmental variable

    export GOOGLE_APPLICATION_CREDENTIALS = ~/.gc/<credentials.json>
    
Use this variable to authenticate cli

    gcloud auth activate-service-account --key-file $GOOGLE_APPLICATION_CREDENTIALS
    
## 1.10 - Downloads

Now that we have our virtual machine we need to install all of the software that we need onto it.  To do this make sure you are connected to the WM instance before continuing.

### 1.10.1 - Python


Go to the Anaconda website and copy the link used to download python on a linux machine
https://www.anaconda.com/

    wget <Anaconda_Download_URL>

Enter the following to finish the installation:

    bash <download_name>
    
### 1.10.2 - Docker

Initiate the download using the following code:

    sudo apt-get install docker.io
    
Update group membership by following these steps:

1. Create Group:
 
    sudo groupadd docker
    
2. Add User
 
    sudo gpasswd -a $USER docker
    
3. Restart Docker

    sudo service docker restart
    
4. Logout of ssh and back in to see the changes take effect

### 1.10.3 - Docker-Compose

Make a new directory and change your drive to it

    mkdir bin
    cd bin

Install the latest linux-x86 64 version of Docker Compose from Git Hub
  
    wget <url> -O docker-compose
    
Make docker-compose exxecutible
  
    chmod +x docker-compose
    
Make docker-compose visible in all directories

 1. enter .bashrc file
 
    nano .bashrc

2. Add the following code to the end of this file:
    
    export PATH ="{HOME}/bin:${PATH}"
    
Refresh

    source .bashrc
    
Download Docker Images

    docker-compose up -d
    
Check the docker images

    docker ps

### 1.10.4 - PGCLI

We need to use Python to install this.  To do this run the following code in the command prompt:
    
    conda install -c conda-forge pgcli
    
Install mycli:
    
    pip install -i mycli    

### 1.10.5 - Terraform

On the Terraform website we need the link for the Linux AMD64 version of Terraform:
https://developer.hashicorp.com/terraform/downloads
Change directory to bin and then download Terraform
    
    cd .bin/
    wget <terraform url>
    
Unzip and remove the zipped file

    unzip <terraform.zip>
    rm <terraform.zip>

## 1.11 - Forward Ports

Pull up the ports tab (ctrl + ~)
Press Forward Port and enter the relevant port numbers

## 1.12 - Initialize Terraform

After our Terraform file has been created, we need to initialize Terraform so we can build our Cloud Environment

    terraform init
