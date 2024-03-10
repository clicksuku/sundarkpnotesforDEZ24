# Learning Data Engineering

## General

-   NY Taxi Data is available as Parquet format

    -   Pandas has functions to convert to CSV which can be easily
        ingested into Pandas Dataframe

-   GCP Account is required for the program. 300 USD free account is
    available for first time users.

    -   Service Account with adequate permissions to GCP Bucket and
        BigQuery

-   Tools required for the program

    -   Anaconda Environment

    -   Pandas

    -   Jupyter Notebook to try commands

    -   Docker Commands to manager containers

    -   Postgres and PGAdmin

    -   Mage AI (Python) for ELT pipelines to move data from Buckets or
        files to BigQuery

    -   Dbt for data pipeline to transform data within Warehouse

    -   GCP BigQuery

    -   GCP Looker Studio for visualization

## Basic Commands

###  Docker Commands

\# Create a Docker Image from a base image

> Docker run -it ubuntu bash
>
>  
>
> #List docker images
>
> Docker images list
>
>  
>
> #List Running containers
>
> Docker ps -a
>
>  
>
> #List with full container ids
>
> Docker ps -a \--no-trunc
>
>  
>
> #Add onto existing image to create new image
>
> Docker commit -a \<User_Name\> -m \"Message\" container_id
> New_Image_Name
>
>  
>
> \# Create a Docker Image with an entrypoint from a base image
>
> Docker run -it \--entry_point=bash python:3.11
>
>  
>
>  
>
> #Attach to a stopped container
>
> Docker start -ai \<Container_Name\>
>
>  
>
> #Attach to a running container
>
> docker exec -it \<Container_ID\> bash
>
>  
>
> #copying from host to container
>
> Docker cp \<SRC_PATH/file\> \<containerid\>:\<dest_path\>
>
>  
>
> #copying from container to host
>
> Docker cp \<containerid\>:\<Srct_path\> \<Dest Path on host/file\>
>
>  
>
>  
>
> #Create an image from a docker file
>
> Docker build -t \<Image_Name\> \<Location of Dockerfile\>
>
>  
>
> #DockerFile Options and best practices
>
> [[https://devopscube.com/build-docker-image/]{.underline}](https://devopscube.com/build-docker-image/)
>
>  
>
>  
>
> #Docker delete all images forcefully
>
> docker rmi -f \$(docker images -aq)
>
>  
>
> #Docker delete all containers forcefully
>
> docker rm -f \$(docker ps -qa)
>
>  
>
> #docker compose creation
>
> [[https://www.composerize.com/]{.underline}](https://www.composerize.com/)

**Docker for PostGreSQL and PGAdmin**

 

> **If Docker is created individually**
>
>  
>
>  
>
> **Network**
>
> Docker network create pg-network
>
>  
>
> **PostGres**
>
> docker run -it \\
>
> -e POSTGRES_USER=\"root\" \\
>
> -e POSTGRES_PASSWORD=\"root\" \\
>
> -e POSTGRES_DB=\"ny_taxi\" \\
>
> -v \$(pwd)/ny_taxi_data:/var/lib/postgresql/data \\
>
> -p 5432:5432 \\
>
> \--network=\"pg-network\" \\
>
> \--name=\"pgdatabase\" \\
>
> postgres:16
>
>  
>
>  
>
> **PGAdmin4**
>
>  
>
> docker run -it \\
>
> -e PGADMIN_DEFAULT_EMAIL=\"admin@admin.com\" \\
>
> -e PGADMIN_DEFAULT_PASSWORD=\"root\" \\
>
> -p 80:80 \\
>
> \--network pg-network \\
>
> \--name pg-admin \\
>
> dpage/pgadmin4
>
>  
>
>  
>
>  
>
> **With Docker Compose**
>
>  
>
>  
>
> services:
>
> pgdatabase:
>
> image: postgres:16
>
> environment:
>
> \- POSTGRES_USER=root
>
> \- POSTGRES_PASSWORD=root
>
> \- POSTGRES_DB=ny_taxi
>
> volumes:
>
> \- \"./ny_taxi_data:/var/lib/postgresql/data:rw\"
>
> ports:
>
> \- \"5432:5432\"
>
> pgadmin:
>
> image: dpage/pgadmin4
>
> environment:
>
> \- PGADMIN_DEFAULT_EMAIL=admin@admin.com
>
> \- PGADMIN_DEFAULT_PASSWORD=root
>
> ports:
>
> \- \"8080:80\"
>
> taxi-ingest:
>
> image: taxi_ingest:v001
>
> command: \--user=root \--password=root \--host=pgdatabase \--port=5432
> \--db=ny_taxi
>
> \--table_name=yellow_taxi_data
> \--url=https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2023-10.parquet

###  GCP Commands

1.  Create SSH Keys

2.  Added to the Settings of Compute Engine VM Instance

3.  SSH-ed into the VM Instance with a config similar to following

>  
>
> Host my-website.com
>
> HostName my-website.com
>
> User my-user
>
> IdentityFile \~/.ssh/id_rsa
>
>  

4.  Installed Anaconda by installing the sh file through bash
    \<Anaconda.sh\>

5.  Install Docker after

    a.  Sudo apt-get update

    b.  Sudo apt-get docker

6.  To run Docker without SUDO permissions

    a.  [[https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md]{.underline}](https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md)

7.  Google cloud remote copy

    a.  gcloud compute scp LOCAL_FILE_PATHVM_NAME:REMOTE_DIR

>  
>
>  
>
>  
>
> Install GCP Cloud SDK on Docker Machine
>
>  
>
> [[https://stackoverflow.com/questions/23247943/trouble-installing-google-cloud-sdk-in-ubuntu]{.underline}](https://stackoverflow.com/questions/23247943/trouble-installing-google-cloud-sdk-in-ubuntu)
>
> sudo apt-get install apt-transport-https ca-certificates gnupg && echo
> \"deb \[signed-by=/usr/share/keyrings/cloud.google.gpg\]
> [[https://packages.cloud.google.com/apt]{.underline}](https://packages.cloud.google.com/apt)
> cloud-sdk main\"\| sudo tee -a
> /etc/apt/sources.list.d/google-cloud-sdk.list&& curl
> [[https://packages.cloud.google.com/apt/doc/apt-key.gpg]{.underline}](https://packages.cloud.google.com/apt/doc/apt-key.gpg)
> \| sudo apt-key \--keyring /usr/share/keyrings/cloud.google.gpg add -
> && sudo apt-get update && sudo apt-get install google-cloud-sdk &&
> sudo apt-get install google-cloud-sdk-app-engine-java && sudo apt-get
> install google-cloud-sdk-app-engine-python && gcloud init

### Anaconda Commands

### 

#Activate environment

> Conda Activate \<environment_name\>
>
>  
>
> #DeActivate environment
>
> Conda DeActivate \<environment_name\>
>
>  
>
> #Start iterm without conda environment
>
> conda config \--set auto_activate_base false
>
>   
>
> \# Using Conda forge as default (Community driven packaging recipes
> and solutions)
>
> [[https://conda-forge.org/docs/user/introduction.html]{.underline}](https://conda-forge.org/docs/user/introduction.html)
>
> conda \--version
>
> conda update conda
>
> conda config \--add channels conda-forge
>
> conda config \--set channel_priority strict
>
>  
>
>  
>
> #Using Libmamba as Solver
>
> conda install pgcli \--solver=libmamba

### 

### Linux/MAC Commands

> **[Starting and Stopping Services on Linux]{.underline}**

-   sudo systemctl start postgresql

-   sudo systemctl stop postgresql

> **[Starting and Stopping Services on MAC]{.mark}**

-   [launchctl start postgresql]{.mark}

-   [launchctl stop postgresql]{.mark}

> **[Identifying processes listening to a Port across
> MAC/Linux]{.underline}** 
>
> sudo lsof -i -P -n \| grep LISTEN
>
> \$ sudo netstat -tulpn \| grep LISTEN
>
> \$ sudo ss -tulpn \| grep LISTEN
>
> \$ sudo lsof -i:22 ## see a specific port such as 22 \##
>
> \$ sudo nmap -sTU -O IP-address-Here
>
>  
>
> **[Installing a package on Debian]{.underline}**
>
>  sudo apt install \<packagename\>
>
>  **[Listing all package on Debian]{.underline}**
>
> Dpkg -l \| grep \<packagename\>
>
>  **[UnInstalling a package on Debian]{.underline}**
>
> Sudo apt remove \<packagename\>
>
> Sudo apt autoclean && sudo apt autoremove
>
> **[List all Processes on Debian/Ubuntu]{.underline}**
>
>  
>
> Ps -aux
>
> apt-get update && apt-get install procps
>
> apt-get install iproute2 for ss -tulpn
>
>  **[#Postgres Install]{.underline}**
>
> sudo sh -c \'echo \"deb
> [[https://apt.postgresql.org/pub/repos/apt]{.underline}](https://apt.postgresql.org/pub/repos/apt)
> \$(lsb_release -cs)-pgdg main\" \> /etc/apt/sources.list.d/pgdg.list\'
>
> wget \--quiet -O -
> [[https://www.postgresql.org/media/keys/ACCC4CF8.asc]{.underline}](https://www.postgresql.org/media/keys/ACCC4CF8.asc)
> \| sudo apt-key add -
>
> sudo apt-get update
>
> sudo apt-get -y install postgresql
>
>  
>
> **[#Changing Postgresql port to 5432]{.underline}**
>
>  
>
> \- sudo service postgresql stop - sed -e \'s/\^port.\*/port = 5432/\'
> /etc/postgresql/10/main/postgresql.conf \> postgresql.conf
>
> \- sudo chown postgres postgresql.conf
>
> \- sudo mv postgresql.conf /etc/postgresql/10/main
>
> \- sudo systemctl restart postgresql
>
>  

# Module 1 -- Docker and Docker compose to create docker images, starting and stopping

-   **[Setting up GCP VM Instance]{.mark}**

    -   [Activated the 300 USD Free tier]{.mark}

    -   [Created a GCP Project]{.mark}

    -   [Created Service Account with permissions to Compute Admin,
        Compute Engine Service Agent]{.mark}

    -   [Created E2-Small VM Instance]{.mark}

    -   [Got the Public IP Address]{.mark}

-   **[Creating the local SSH Key]{.mark}**

    -   [[[https://cloud.google.com/compute/docs/connect/create-ssh-keys#console]{.underline}]{.mark}](https://cloud.google.com/compute/docs/connect/create-ssh-keys#console)

    -   [ssh-keygen -t rsa -f \~/.ssh/KEY_FILENAME-C USERNAME-b
        2048]{.mark}

    -   [Cat keyname.pub \| pbcopy]{.mark}

-   [SSHed into Google VM with public iP Address]{.mark}

> [Created a Config for easy VM]{.mark}
>
> [Host [[my-website.com]{.underline}](http://my-website.com/)]{.mark}
>
> [HostName [[my-website.com]{.underline}](http://my-website.com/)]{.mark}
>
> [User my-user]{.mark}
>
> [IdentityFile \~/.ssh/id_rsa]{.mark}

-   [Installed Anaconda]{.mark}

    -   [Sudo apt-get update]{.mark}

    -   [Wget 
        [[https://repo.anaconda.com/archive/Anaconda3-2023.09-0-Linux-x86_64.sh]{.underline}](https://repo.anaconda.com/archive/Anaconda3-2023.09-0-Linux-x86_64.sh)]{.mark}

    -   [Bash [[Anaconda3-2023.09-0-Linux-x86_64.sh]{.underline}](http://anaconda3-2023.09-0-linux-x86_64.sh/)]{.mark}

    -   [Post Installing Conda, if Conda command is not found, please
        try]{.mark}

        -   [source \~/anaconda3/etc/profile.d/conda.sh]{.mark}

    -   [Adding Code-Forge (Community repository of recipes) as a
        Channel and LibMamba as Solver for faster search of Conda
        Repository]{.mark}

> [conda update conda]{.mark}
>
> conda config \--add channels conda-forge
>
> conda config \--set channel_priority strict
>
> [ ]{.mark}
>
> [#Using Libmamba as Solver]{.mark}
>
> [conda install pgcli  \--solver=libmamba]{.mark}

-   [If PIP Install pgcli gives issue, conda install can be used to
    install pgcli]{.mark}

> [ ]{.mark}

-   [Install Docker]{.mark}

    -   [Sudo apt-get
        install [[docker.io]{.underline}](http://docker.io/)]{.mark}

    -   [Running Docker without sudo]{.mark}

        1.  [To run Docker without SUDO permissions]{.mark}

            1.  [[[https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md]{.underline}]{.mark}](https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md)

> [ ]{.mark}

-   [Install Docker Compose]{.mark}

    -   [Install Docker compose from github release location 
        [[https://github.com/docker/compose/releases/tag/v2.24.2]{.underline}](https://github.com/docker/compose/releases/tag/v2.24.2)]{.mark}

    -   [Add Path to bin]{.mark}

-   [Install PGCLI]{.mark}

    -   [If PIP Install PGCLI gives error, Here pgcli is installed from
        conda-forge with conda install -c conda-forge pgcli]{.mark}

    -   [Followed by \"pip install -U mycli\"]{.mark}

-   [Leverage Docker Compose to install Postgres and PGAdmin]{.mark}

> [ ]{.mark}
>
> [services:]{.mark}
>
> [  pgdatabase1:]{.mark}
>
> [    image: postgres:16]{.mark}
>
> [    environment:]{.mark}
>
> [      - POSTGRES_USER=root]{.mark}
>
> [      - POSTGRES_PASSWORD=root]{.mark}
>
> [      - POSTGRES_DB=ny_taxi1]{.mark}
>
> [    volumes:]{.mark}
>
> [      - \"./ny_taxi1_data:/var/lib/postgresql/data:rw\"]{.mark}
>
> [    ports:]{.mark}
>
> [      - \"5432:5432\"]{.mark}
>
> [    restart: always]{.mark}
>
> [  pgadmin1:]{.mark}
>
> [    image: dpage/pgadmin4]{.mark}
>
> [    environment:]{.mark}
>
> [      - PGADMIN_DEFAULT_EMAIL=[[admin@admin.com]{.underline}](mailto:admin@admin.com)]{.mark}
>
> [      - PGADMIN_DEFAULT_PASSWORD=root]{.mark}
>
> [    ports:]{.mark}
>
> [      - \"8080:80\"]{.mark}
>
> [    restart: always]{.mark}
>
> [ ]{.mark}

-   [Leverage Docker Compose to install Postgres and PGAdmin]{.mark}

# 

# Module 2 -- Workflow Orchestration

-   Workflow Orchestration helps setting up a Workflow pipeline for Data
    Load/Ingestion, transform and load

-   Mage.AI supports connectors to various data sources and has support
    for languages such as Python, SQL and R

-   With Mage, in this module, following were the exercises

    -   Setup One Pipeline

        -   Loading the data from CSV or Parquet

        -   Transform step to clean the data to remove tables with
            passenger count 0

        -   Data Exporter to Postgresql

        -   Data Exporter to S3 bucket

        -   Data Exporter to S3 bucket (Partitioned -- tpep_pickup_time)

        -   Data Exporter to S3 bucket (Partitioned and Clustered --
            Cluster by Vendor ID)

        -   Partition and Cluster help increase the speed when the data
            is high

    -   Another pipeline

        -   Load data from S3 bucket

        -   Make column names lower

        -   Load it into BQ

# 

# 

# 

# 

# 

# Module 3 -- Data Warehouse (GCP BQ)

-   Workflow Orchestration helps setting up a Workflow pipeline for Data
    Load/Ingestion, transform and load

-   Clustering vs Partitioning in BQ

-   Creating Partitioned and Clustered tables for Yellow taxi data

> ![A white sheet with black text Description automatically
> generated](media/image1.png){width="3.0466491688538935in"
> height="1.4892322834645668in"}

-   BQ Best practices

> ![A white text on a white background Description automatically
> generated](media/image2.png){width="2.2442541557305336in"
> height="1.1619094488188977in"}
>
> ![A white background with black text Description automatically
> generated](media/image3.png){width="3.560254811898513in"
> height="0.8405621172353456in"}

-   BQ Internals -- Uses column oriented structure

    -   Colossus -- Cheap storage in Columnar format

    -   Dremel -- Compute -- Query execution engine which executes each
        sub query

    -   Jupiter -- High speed network -- 1 TBPS

    -   Borg -- Orchestrator precursor to Kubernetes

![A screenshot of a computer Description automatically
generated](media/image4.png){width="2.0208366141732284in"
height="2.078151793525809in"}

-   In BQ, a model was created to predict Tips

    -   A table was created to support Model with columns of interest
        such as Pickup Location, Drop Location, Payment type, Fare
        Amount, Passenger count, tip amount, tolls amount.

    -   A default Model with 'Linear Regression' created with input as
        'Tips Amount'

    -   ML -- Feature_Info

    -   ML -- Evaluate

    -   ML -- Explain_Predict

-   Model Deployment

    -   Extract the model into a GS Bucket

    -   In the GCP VM, copy from bucket to a temp folder

    -   Pull Tensorflow serving to host the model

    -   Call the model with Postman to test the model

![A screen shot of a computer Description automatically
generated](media/image5.png){width="4.749004811898513in"
height="1.9162281277340332in"}

![A diagram of a software Description automatically
generated](media/image6.png){width="5.719330708661417in"
height="3.0028707349081363in"}

# Module 4 -- Analytics Engineering

-   Data Processing with DBT

![A screenshot of a computer Description automatically
generated](media/image7.png){width="6.268055555555556in"
height="2.7472222222222222in"}

DBT helps with 'T' of the ELT part. Traditionally, ETL was done
(Extract, Transform and Load) but with Modern Data stack, it is ELT in
which data is extracted from various places, loaded/ingested into Data
Lake or Warehouses. And transformation happens with DBT.

One of the confusions I had was, the data steps were like whatever was
done with Mage AI. For example, could not have I achieved creating the
Facts and everything from Mage itself instead of DBT (Data Build tool).
Then my limited understanding was the following

-   DBT Modelling is centered around Data Analysts.

-   Language is SQL.

-   The joins, tables or Views and creation of facts happen within the
    data lake.

-   DBT helps with following.

    -   Version Control like Coding in another language

    -   Helps with Lineage, which would be a big thing if data sources
        are disparate, and the flow is complex.

-   Reusable Macros

-   DBT can be a step in orchestrators such as Mage AI or AirFlow or
    Perfect.

**Steps done in this exercise.**

-   Created a Cloud IDE Project (Non-Enterprise are limited to one
    project in Cloud DBT)

-   Connected to Github Account

-   Connected to BigQuery

-   The project had the following structure when created

    -   ![](media/image8.png){width="2.6527777777777777in"
        height="2.9305555555555554in"}

-   In dbt_project.yml, changed the Project_name in

    -   Name

    -   Models

-   Added a Staging folder to Models. Created schema.yml

    -   Under Schema, imported the two tables from BigQuery under
        Sources. (There is Auto-complete)

> ![A screenshot of a computer code Description automatically
> generated](media/image9.png){width="3.8055555555555554in"
> height="3.0in"}

-   Generate Model helped generate the model for the two tables from
    above.

-   Once generated, Lineage is created with SRC (Source) and Model.

```{=html}
<!-- -->
```
-   JINJA templates

    -   DBT uses Jinja templates to resolve. {{ }}

    -   {{ ref() }} refers to data sources

    -   {% %} for expressions, statements

    -   {% macro %} for macros.

-   Three folders in DBT project are important

    -   Models

    -   Macros

    -   Seeds

-   Macros are User-Defined functions which can be called from Models

    -   Get_Payment_description macro is created and added to Green Cab
        Data model and Yellow Cab Data model.

-   Seeds is a folder, in which raw files can be loaded at Project
    initiation. In this case, Taxi_Zone_Lookup.csv is added.

-   Dbt_utils and dbt codegen are packages in dbt with lots of custom
    functions. Dbt deps imports all the functions after including the
    following packages. Code gen helps generating schema on the fly.

> ![A screenshot of a computer code Description automatically
> generated](media/image10.png){width="3.736111111111111in"
> height="1.4444444444444444in"}

-   Create a Core folder under Models. Create the following.

    -   Dim_Zones

    -   Fact_trips

-   Adding tests and documentation for the DBT

    -   Dbt docs help create documentation for the DBT package

    -   Observability can be added.

-   Setup Nightly builds for DBT to ensure data freshness and facts are
    created based on recent data

# Module 5

-   Data Processing

    -   Batch Processing

    -   Streaming Processing
