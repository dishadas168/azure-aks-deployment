# Terraform AKS Cluster Deployment on Azure

This repository contains Terraform manifests for deploying an Azure Kubernetes Service (AKS) cluster with a Linux node pool. Additionally, it sets up a Blob Storage in Azure to store the Terraform state file (`tfstate`) securely.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Customization](#customization)
- [Usage](#usage)
- [Deploying AKS Cluster](#deploying-aks-cluster)
- [Applying Kube Manifests](#applying-kube-manifests)
- [Destroying Terraform Cluster](#destroying-terraform-cluster)

## Prerequisites

Before using these Terraform manifests, make sure you have the following prerequisites in place:

- **Azure CLI**: You must have Azure CLI installed and configured. You can download it from [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

- **Terraform**: Install Terraform on your local machine. You can find the installation guide [here](https://learn.hashicorp.com/tutorials/terraform/install-cli).

- **Kubectl**: Make sure you have `kubectl` installed to manage your Kubernetes cluster. You can find the installation guide [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

- **Create SSH Public Key for Linux VMs**: Run the following commands to create an SSH Public key. This public key is referenced in `02-variables.tf` file.

```bash
# Create Folder
mkdir $HOME/.ssh/aks-prod-sshkeys-terraform

# Create SSH Key
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/aks-prod-sshkeys-terraform/aksprodsshkey \
    -N mypassphrase

# List Files
ls -lrt $HOME/.ssh/aks-prod-sshkeys-terraform
```

## Customization

You can customize the Terraform deployment by modifying the following files:

- `01-main.tf`: Customize the Azure resource group, storage account, container, and key for storing the Terraform state. Replace the default values with your own.
- `02-variables.tf`: Customize Azure Region, environment (Dev or Prod), resource group name in which the AKS instance resides
- `07-aks-cluster.tf`: Customize the AKS cluster settings
- `09-aks-cluster-linux-user-nodepools.tf`: Customize the Linux Nodepool settings

## Usage

1. Clone this repository to your local machine:

   ```bash
   git clone https://github.com/dishadas168/azure-aks-deployment.git
   ```

2. Navigate to the project directory:

   ```bash
   cd terraform-manifests-aks
   ```

## Deploying AKS Cluster

To deploy the AKS cluster using Terraform, follow these steps:

```bash
# Initialize Terraform from this folder
terraform init

# Validate Terraform manifests
terraform validate

# Review the Terraform Plan
terraform plan

# Deploy Terraform manifests
terraform apply 
```
Wait for Terraform to complete the deployment.

## Applying Kube Manifests

Once the AKS cluster is up and running, use `kubectl` to apply Kubernetes manifests to the cluster. Place the Kubernetes manifests (YAML files) in `kube-manifests` directory and use the following commands to apply them:

```bash
# Configure `kubectl` to use your AKS cluster:
az aks get-credentials --resource-group terraform-aks-dev --name terraform-aks-dev-cluster --admin

# Navigate to the directory containing your Kubernetes manifests folder kube-manifests
cd ..

# Apply the manifests to your AKS cluster:
kubectl apply -R -f kube-manifests/

```
The AKS cluster is now configured and ready to run our applications.

## Destroying Terraform Cluster

```bash
# Change directory to terraform-manifests-aks
cd terraform-manifests-aks

# Destroy all Terraform Resources
terraform destroy

```

## Credits and Acknowledgments

- The AKS deployment script is based on [azure-aks-kubernetes](https://github.com/stacksimplify/azure-aks-kubernetes-masterclass.git)
