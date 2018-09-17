# Provision Kubernetes Cluster

To reduce costs we will use one cluster for the following execution environments:

- development
- staging
- production

To garantee isolation between envrionments we will use ``Namespaces`` and probably ``Network Policies``. This will let us explore and test multi-tenency in a Kubernetes cluster at cost of more complex configuration.

Later, we will use dedicated kubernetes clusters and even dedicated GCP projects for better isolation between environments.

## Create the Kubernetes Cluster

- Create a project. We'll assume that the projct name is $PROJECT_NAME

- Replace in ``terraform.tfvars`` the project property with $PROJECT_NAME (you may also change the zone to your favorite one)

- Initialize ``gcloud`` configuration by selecting your active account, active project and your favorite zone.

````bash
gcloud init
````

- Get your *application defaut credentials*. They will be used by terraform.

````bash
gcloud auth application-default login
````

- Run Terraform

````bash
terraform init
````

````bash
terraform plan
````

````bash
terraform apply
````

Verify:

````bash
gcloud container clusters list
````

````bash
NAME        LOCATION        MASTER_VERSION  MASTER_IP       MACHINE_TYPE   NODE_VERSION  NUM_NODES  STATUS
playground  europe-west1-b  1.9.7-gke.6     XX.XX.XX.XX  n1-standard-1  1.9.7-gke.6   3          RUNNING
````

## Grant the Cloud Builder Service Account access to the cluster

Cloud Builder will use the Kubernetes API to run the ``kubectl`` command. The service account is identified by an email in the following format : ${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com

````bash
PROJECT_NUMBER=$(gcloud projects describe ${PROJECT_ID} --format='value(projectNumber)')

gcloud projects add-iam-policy-binding ${PROJECT_NUMBER} \
  --member=serviceAccount:${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com \
  --role=roles/container.developer

````
