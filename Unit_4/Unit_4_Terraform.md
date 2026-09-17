Unit IV — Terraform
Overview

Terraform is an Infrastructure as Code (IaC) tool used to define, provision, and manage infrastructure using configuration files.

Terraform configurations are written using HashiCorp Configuration Language (HCL). Instead of manually creating resources, Terraform allows infrastructure to be described in configuration files and then creates or updates those resources based on the configuration.

This unit covers Terraform fundamentals, providers, resources, variables, outputs, Terraform workflow, state management, dependencies, and practical use of Terraform commands.

1. Introduction to Terraform
1.1 What is Terraform?

Terraform is an Infrastructure as Code tool used to create and manage infrastructure through configuration files.

Terraform configuration files describe the desired state of infrastructure.

Terraform compares the desired configuration with the current state and determines what changes are required.

Basic Terraform Workflow
Terraform Configuration
        ↓
   terraform init
        ↓
  terraform validate
        ↓
    terraform plan
        ↓
   terraform apply
        ↓
 Infrastructure Created
        ↓
  Terraform State
2. Infrastructure as Code

Infrastructure as Code means managing infrastructure using code or configuration files rather than performing all configuration manually.

Advantages
Infrastructure can be reproduced
Configuration can be version controlled
Changes can be reviewed
Deployment becomes repeatable
Manual configuration is reduced
Infrastructure changes can be tracked
3. Terraform Configuration Files

Terraform configuration files normally use the .tf extension.

Example:

main.tf

A Terraform project can contain multiple .tf files.

Example:

terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfstate

Terraform loads configuration from the .tf files in the working directory.

4. Terraform Providers

A provider allows Terraform to interact with a particular platform or service.

Examples include providers for:

AWS
Azure
Google Cloud
Kubernetes
Docker
Local resources

For the practical experiment, the Local provider is used.

Example:

terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}

provider "local" {}