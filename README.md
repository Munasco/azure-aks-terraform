# Azure Kubernetes Service (AKS) Terraform Configuration

This Terraform configuration creates an Azure Kubernetes Service (AKS) cluster with the following specifications:

## Features

- **AKS Cluster** with System Assigned Managed Identity
- **Node Pool** with 2 nodes using Standard VMs
- **Storage Profile** with all drivers enabled:
  - Blob Driver
  - File Driver
  - Disk Driver
  - Snapshot Controller
- **Auto-scaling** capability (1-3 nodes)
- **Azure Policy** add-on enabled
- **RBAC** enabled
- **Network Profile** with kubenet plugin

## Prerequisites

Before running this Terraform configuration, ensure you have:

1. **Azure CLI** installed and configured
   ```bash
   az login
   az account set --subscription "your-subscription-id"
   ```

2. **Terraform** installed (version >= 1.0)
   ```bash
   terraform version
   ```

3. **Appropriate Azure permissions** to create:
   - Resource Groups
   - AKS Clusters
   - Network resources
   - Storage resources

## File Structure

```
azure-aks-terraform/
├── main.tf           # Main Terraform configuration
├── variables.tf      # Variable definitions
├── outputs.tf        # Output definitions
├── terraform.tfvars  # Variable values (customize as needed)
└── README.md         # This file
```

## Configuration Files

### main.tf
Contains the main resource definitions for:
- Resource Group
- AKS Cluster with all required specifications
- System Assigned Identity configuration

### variables.tf
Defines all input variables with:
- Descriptions
- Type constraints
- Default values
- Validation rules

### outputs.tf
Defines output values including:
- Cluster information
- Connection details (sensitive)
- Identity information

### terraform.tfvars
Contains variable values that can be customized:
- Azure region
- VM size
- Node count
- Scaling parameters
- Resource tags

## Deployment Steps

### 1. Clone or Download
```bash
# If using git
git clone <repository-url>
cd azure-aks-terraform

# Or download and extract the files to a directory
```

### 2. Initialize Terraform
```bash
terraform init
```

### 3. Review and Customize Variables
Edit `terraform.tfvars` to match your requirements:
```bash
# Example customizations
location = "West US 2"
node_count = 3
vm_size = "Standard_D4s_v3"
```

### 4. Plan the Deployment
```bash
terraform plan
```

### 5. Apply the Configuration
```bash
terraform apply
```
Type `yes` when prompted to confirm the deployment.

### 6. Get Cluster Credentials
After deployment, configure kubectl to use the new cluster:
```bash
# Get cluster credentials
az aks get-credentials --resource-group $(terraform output -raw resource_group_name) --name $(terraform output -raw cluster_name)

# Verify connection
kubectl get nodes
```

## Important Outputs

After successful deployment, you'll have access to:

- `cluster_name` - Name of the AKS cluster
- `resource_group_name` - Name of the resource group
- `cluster_fqdn` - Fully qualified domain name
- `system_assigned_identity_principal_id` - Principal ID of the managed identity
- `kube_config` - Raw Kubernetes configuration (sensitive)

## Customization Options

### VM Sizes
Common VM sizes available in the configuration:
- `Standard_D2s_v3` (2 vCPUs, 8 GB RAM) - Default
- `Standard_D4s_v3` (4 vCPUs, 16 GB RAM)
- `Standard_B2s` (2 vCPUs, 4 GB RAM) - Burstable

### Azure Regions
Popular regions supported:
- `East US`
- `West US 2`
- `Central US`
- `West Europe`
- `Southeast Asia`

### Scaling Configuration
- `enable_auto_scaling` - Enable/disable auto-scaling
- `min_count` - Minimum nodes (1-5)
- `max_count` - Maximum nodes (2-20)

## Security Considerations

1. **System Assigned Identity** - The cluster uses Azure managed identity for secure access to Azure resources
2. **RBAC Enabled** - Role-based access control is enabled for fine-grained permissions
3. **Network Security** - Uses kubenet plugin for network isolation
4. **Storage Security** - All storage drivers are enabled with secure defaults

## Cost Optimization

- Uses `Standard_D2s_v3` VMs by default (cost-effective)
- Auto-scaling enabled to optimize costs based on demand
- Configurable node count and VM sizes
- Resource tagging for cost tracking

## Cleanup

To destroy all created resources:
```bash
terraform destroy
```
Type `yes` when prompted to confirm the destruction.

## Troubleshooting

### Common Issues

1. **Authentication Errors**
   ```bash
   az login
   az account show  # Verify correct subscription
   ```

2. **Quota Limitations**
   - Check Azure subscription quotas
   - Try different VM sizes or regions
   - Contact Azure support for quota increases

3. **Resource Naming Conflicts**
   - The configuration uses random suffixes to avoid conflicts
   - If issues persist, modify the `terraform.tfvars` file

4. **Network Issues**
   - Ensure no conflicting network configurations
   - Check Azure region availability

### Validation Commands

```bash
# Validate Terraform configuration
terraform validate

# Format Terraform files
terraform fmt

# Show current state
terraform show

# List all resources
terraform state list
```

## Support

This configuration follows Terraform and Azure best practices. For issues:

1. Check Azure service health
2. Verify subscription permissions
3. Review Terraform logs
4. Consult Azure AKS documentation

## Version Information

- **Terraform**: >= 1.0
- **AzureRM Provider**: ~> 3.0
- **Random Provider**: ~> 3.0
- **Kubernetes Version**: Latest stable (configurable)

---

**Note**: Always review and test configurations in a non-production environment first.
