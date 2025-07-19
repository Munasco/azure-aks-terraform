# Quick Deployment Guide

## Step-by-Step Deployment

### 1. Prerequisites Check
```bash
# Check if Azure CLI is installed
az version

# Check if Terraform is installed
terraform version

# Check if kubectl is installed (optional, for cluster access)
kubectl version --client
```

### 2. Azure Authentication
```bash
# Login to Azure
az login

# Set the correct subscription (if you have multiple)
az account list --output table
az account set --subscription "your-subscription-id"

# Verify current subscription
az account show
```

### 3. Prepare Variables
```bash
# Copy the example variables file
cp terraform.tfvars.example terraform.tfvars

# Edit the variables file with your preferred values
# Use your preferred editor (nano, vim, code, etc.)
nano terraform.tfvars
```

### 4. Deploy Infrastructure
```bash
# Initialize Terraform (downloads providers)
terraform init

# Review what will be created
terraform plan

# Deploy the infrastructure
terraform apply
# Type 'yes' when prompted
```

### 5. Get Cluster Access
```bash
# Get cluster credentials
az aks get-credentials \
  --resource-group $(terraform output -raw resource_group_name) \
  --name $(terraform output -raw cluster_name) \
  --overwrite-existing

# Test cluster access
kubectl get nodes
kubectl get all --all-namespaces
```

### 6. Verify Deployment
```bash
# Show all Terraform outputs
terraform output

# Get specific output values
terraform output cluster_name
terraform output cluster_fqdn
terraform output system_assigned_identity_principal_id
```

## Expected Deployment Time
- **Planning**: 1-2 minutes
- **Deployment**: 10-15 minutes
- **Total**: ~15-20 minutes

## Post-Deployment Actions

### Install Sample Application (Optional)
```bash
# Deploy a sample nginx application
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# Get the external IP (may take a few minutes)
kubectl get services --watch
```

### View Resources in Azure Portal
1. Navigate to Azure Portal
2. Go to Resource Groups
3. Find your resource group (rg-aks-xxxxx)
4. Explore the created resources

## Cleanup (When Done)
```bash
# Destroy all resources
terraform destroy
# Type 'yes' when prompted

# Remove kubectl context (optional)
kubectl config get-contexts
kubectl config delete-context [context-name]
```

## Troubleshooting Quick Fixes

### Authentication Issues
```bash
# Clear Azure CLI cache
az account clear

# Re-login
az login
```

### Terraform State Issues
```bash
# Refresh state
terraform refresh

# Import resources if needed
terraform import azurerm_resource_group.aks_rg /subscriptions/.../resourceGroups/rg-name
```

### Quota Issues
```bash
# Check current quota usage
az vm list-usage --location "East US" --output table

# Request quota increase through Azure Portal if needed
```

---

**Note**: Keep your `terraform.tfstate` file secure as it contains sensitive information about your infrastructure.
