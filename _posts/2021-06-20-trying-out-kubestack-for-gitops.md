---
categories: Azure
tags: ['Terraform', 'DevOps', 'InfrastructureAsCode', 'GitOps', 'Kubernetes' , 'GCP']
is_post: "True"
is_home_btn_reqd: "True"
---

I recently came across `Kubestack` which is a GitOps framework for Infra automation for kubernetes.   
- Kubestack currenlty supports AWS, AZURE and GCP . 
- Kubestack has it's own terraform provider as well which is supported by `Kubestack` team.

In my current project, we are using GCP so thought of trying out the [tutorial](https://www.kubestack.com/framework/documentation/tutorial-get-started) for GKE (GCP managed kubernetes service).

The tutorial is very well documented and have clear instructions for multiple OS including Linux , macOS and Windows .   
But tutorial was a bit outdated for GKE and had to make few changes for getting it to work. Also the documentation lacks the 1 click code/commands snippet "Copy" function :clipboard: .

Things that need to be updated which were not specified in documentation:
- By default , GKE tutorial git repo has k8s version as `1.16` but GKE doesn't support that as of now therefore i had to update `cluster_min_master_version` in kubestack-starter-gke/gke_zero_cluster.tf to `1.19`.
- Also to get the tutorial to work below API's also need to be enabled in GCP console:
  - Identity and Access Management (IAM) API
  - Cloud DNS API
  - Kubernetes Engine API
  - Cloud Resource Manager API


Initially i tried the `macOS` instructions but was blocked due to the "Internet Security" enabled on my macbook pro. Below is the error which i got:
```
 Error: Error accessing remote module registry
│ 
│ Failed to retrieve available versions for module "gke_zero_nginx" (gke_zero_ingress.tf:1) from kbst.xyz: Failed to request discovery document: Get
│ "https://kbst.xyz/.well-known/terraform.json": x509: certificate signed by unknown authority.
```

Instead of troubleshooting the macOS issue i spun up an `Ubuntu, 20.04 LTS` VM instance on GCP to continue the tutorial with Linux instructions. 

Tutorial is categorized in 3 categories, mainly:
- Local Development
- Infrastructure provisioning
- Configuring Automation

# Local Development
```
# Download the latest release
curl -LO "https://github.com/kbst/kbst/releases/download/$(curl -s https://www.kubestack.com/cli-latest.txt)/kbst_linux_amd64.zip"

# Extract the binary into your PATH e.g. /usr/local/bin
sudo unzip -d /usr/local/bin/ kbst_linux_amd64.zip kbst

# Verify the binary works
kbst --version
```
I tried the kbst version `v0.1.5`

```
# Use the kbst CLI to scaffold your repository using the GKE starter
kbst repo init gke

# Change into the starter directory
cd kubestack-starter-gke
```
This repository includes all the files required for tutorial to work and just need to update few variables that gives you control on naming and region of GCP resources .


Best part of `kubestack` is that you can try out the framework on your local and it helps you provision local cluster by using it's CLI:
```
kbst local apply
```

If for some reason, you get some errors and you need to start from scratch or you are done with tutorial, just do the below:
```
kbst local destroy
```

# Infrastructure provisioning

Another great thing while trying this tutorial is that the `kubestack` team have provided a docker image preinstalled with required tools to try out this tutorial.   
Run below to create the container:
```
# Build the bootstrap container
docker build -t kbst-infra-automation:bootstrap .

# Exec into the bootstrap container
docker run --rm -ti \
    -v `pwd`:/infra \
    kbst-infra-automation:bootstrap
```
When the 2nd command finishes, terminal will be switched to the container terminal.   
Next up, we would need to create a GCP IAM service account which will be used in GitHub Actions.

```
gcloud init
```
Here, just follow the instructions to configure with respective gcp project.

```
# Use the current project
PROJECT=$(gcloud config get-value project)

# Create the service account
gcloud iam service-accounts create kubestack-automation \
  --description "SA used for Kubestack Github Actions" \
  --display-name "kubestack-automation"
  
# Assign the owner role to the service account
gcloud projects add-iam-policy-binding ${PROJECT} \
  --member serviceAccount:kubestack-automation@${PROJECT}.iam.gserviceaccount.com \
  --role roles/owner

# Create service account keys
gcloud iam service-accounts keys create \
  ~/.config/gcloud/application_default_credentials.json \
  --iam-account kubestack-automation@${PROJECT}.iam.gserviceaccount.com
  
# Activate the service account and stop using your personal account  
gcloud auth activate-service-account --key-file ~/.config/gcloud/application_default_credentials.json
```

As per terraform best practice, we need to save terraform state in a remote state backend . Here we will use GCP storage bucket .

```
# Set the location of your multi-regional bucket
# valid values are `asia`, `eu` or `us`
read -p "Bucket location: " LOCATION

```

```
# Create bucket and configure remote state
BUCKET_NAME=terraform-state-kubestack-`git rev-parse --short HEAD`
gsutil mb -l $LOCATION gs://$BUCKET_NAME
gsutil versioning set on gs://$BUCKET_NAME
cat > state.tf <<EOF
terraform {
  backend "gcs" {
    bucket = "${BUCKET_NAME}"
  }
}
EOF
```

Now that we have completed the bootstrap setup, we are good to run terraform commands which will provision required resources.

```
# Initialize Terraform
terraform init

# Create apps and ops workspaces
terraform workspace new apps
terraform workspace new ops

# Bootstrap the ops environment
terraform apply --auto-approve --target module.gke_zero
terraform apply --auto-approve

# Bootstrap the apps environment
terraform workspace select apps
terraform apply --auto-approve --target module.gke_zero
terraform apply --auto-approve
```
References:
- https://www.kubestack.com/
- https://www.kubestack.com/framework/documentation/tutorial-get-started