# Terraform Azure VM Deployment

## Overview
This repository contains a Terraform script to deploy a Linux VM in Azure with:
- Azure Key Vault for credential storage
- Network security groups for secure access
- Automated password generation using `random_password`
- Parameterized configurations for multiple environments

## Why was this specific VM type chosen?
- `Standard_B1s` was chosen for **cost-efficiency** and **low resource usage**.
- It can be **upgraded** easily if more resources are needed.

## Were all necessary resources created to ensure the VM is accessible?
✅ Yes, including:
- **Virtual Network & Subnet**
- **Public IP & Network Security Group**
- **SSH Access (Port 22)**

## Is the Terraform code parameterized for reuse?
✅ Yes, it uses **variables** and **tfvars** for different environments.

## How to Run?
1. Clone the repo:
   ```bash
   git clone https://github.com/YOUR_GITHUB_USERNAME/terraform-azure-vm.git
   cd terraform-azure-vm
