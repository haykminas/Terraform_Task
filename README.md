# Terraform Azure VM Deployment

## Overview
This repository contains a Terraform script to deploy a Linux VM in Azure with:
- Azure Key Vault for credential storage
- Network security groups for secure access
- Automated password generation using `random_password`
- Parameterized configurations for multiple environments

## Questions & Answers

### 1️⃣ Why was this specific VM type chosen?
- The VM type `Standard_B1s` was selected because it is **cost-effective** and **suitable for monitoring tools**.
- It provides **adequate performance** for basic workloads while keeping costs low.
- If needed, it can be **easily upgraded** to a higher performance instance.

### 2️⃣ Were all necessary resources created to ensure the VM is accessible?
✅ Yes, the Terraform script provisions:
- **Networking Resources:**
  - Virtual Network (VNet) and Subnet for private connectivity.
  - Public IP for external access (if required).
  - Network Security Group (NSG) to allow SSH (Port 22).
- **Security Features:**
  - Azure Key Vault to store secrets securely.
  - Strong password generated using `random_password`.

### 3️⃣ Is the Terraform code parameterized to allow reuse across different environments?
✅ Yes! The code uses **Terraform variables** for flexibility:
- The VM name, size, and region are all configurable via `variables.tf`.
- Different environments (Dev, Staging, Prod) can use **separate `.tfvars` files**:
  ```sh
  terraform apply -var-file="dev.tfvars"
  terraform apply -var-file="prod.tfvars"
  ```

### 4️⃣ How can it be ensured that the VM is managed exclusively through Terraform?
To ensure Terraform exclusively manages the VM:
1. **Use Remote State Storage:** Store Terraform state in an **Azure Storage Account**:
   ```hcl
   terraform {
     backend "azurerm" {
       resource_group_name  = "terraform-state-rg"
       storage_account_name = "tfstatestorage"
       container_name       = "tfstate"
       key                  = "linux-vm.tfstate"
     }
   }
   ```
2. **Restrict Manual Changes:** Enforce Azure Policy to prevent manual modifications.
3. **Detect Drift Regularly:** Use `terraform plan` to identify changes made outside Terraform.

### 5️⃣ What modifications are needed to make the code suitable for a team setup?
For **team collaboration**, the following modifications are recommended:
- **Use Remote State Storage** → Ensures consistency when multiple people apply changes.
- **Modularize Terraform Code** → Split into `network.tf`, `vm.tf`, `keyvault.tf` for better maintainability.
- **Automate CI/CD** → Use GitHub Actions or Azure DevOps to automatically apply Terraform changes.

### 6️⃣ How can the correct order of creating interdependent resources be ensured?
Terraform automatically handles dependencies, but explicit control can be added:
- **Implicit Dependencies** → Terraform resolves dependencies based on resource references:
  ```hcl
  resource "azurerm_network_interface" "nic" {
    subnet_id = azurerm_subnet.subnet.id  # Ensures subnet is created first
  }
  ```
- **Explicit Dependencies** → Use `depends_on` to enforce order:
  ```hcl
  resource "azurerm_linux_virtual_machine" "vm" {
    depends_on = [azurerm_network_interface.nic]
  }
  ```

### 7️⃣ How can this code be executed automatically? Which Terraform commands make sense in which scenarios?
- **Local Execution:**
  ```sh
  terraform init
  terraform plan
  terraform apply
  ```
- **CI/CD Pipelines (GitHub Actions, Azure DevOps):**
  - Run `terraform plan` on Pull Requests.
  - Run `terraform apply` when merged to `main`.
  
  **Example GitHub Actions Workflow:**
  ```yaml
  name: Terraform Deployment
  on:
    push:
      branches:
        - main
  jobs:
    terraform:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v2
        - uses: hashicorp/setup-terraform@v1
        - run: terraform init
        - run: terraform plan
        - run: terraform apply -auto-approve
  ```

### 8️⃣ What are the advantages and disadvantages of using Terraform?
| **Pros** | **Cons** |
|----------|----------|
| **Infrastructure as Code (IaC)** → Repeatability and version control | **State Management Complexity** → Requires proper state storage |
| **Multi-Cloud Support** → Works with AWS, Azure, GCP | **Limited Procedural Logic** → Cannot handle complex loops easily |
| **Dependency Management** → Creates resources in the right order | **Manual Drift Handling** → Requires `terraform plan` to detect drift |
| **Modular and Reusable** → Supports Modules and Variables | **No Native GUI** → Requires third-party tools for visualization |
| **Secure by Design** → Supports secret management via **Key Vault** | **Learning Curve** → Requires knowledge of HCL syntax and Terraform state |

---

## 🚀 How to Run?
1. Clone the repository:
   ```sh
   git clone https://github.com/haykminas/Terraform_Task.git
   cd Terraform_Task
   ```
2. Initialize Terraform:
   ```sh
   terraform init
   ```
3. Plan and Apply:
   ```sh
   terraform plan
   terraform apply -auto-approve
   ```
