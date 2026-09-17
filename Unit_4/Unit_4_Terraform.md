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

5. Terraform Required Providers

The required_providers block specifies the providers required by a Terraform configuration.

Example:

terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}
Important

The correct block name is:

required_providers

and not:

requiredproviders

Using the incorrect block name results in an error such as:

Unsupported block type
Did you mean "required_providers"?
6. Terraform Provider Block

A provider block configures the provider.

Example:

provider "local" {}

For the Local provider, no additional configuration is required.

7. Terraform Resources

Resources represent infrastructure objects managed by Terraform.

Example:

resource "local_file" "profile" {
  filename = "student_profile.txt"

  content = "Student Name: ${var.student_name}\nThis profile is created by Terraform."
}

Here:

local_file

is the resource type.

profile

is the resource name.

The complete resource address is:

local_file.profile

8. Terraform Variables

Variables allow values to be passed into a Terraform configuration.

Example:

variable "student_name" {
  default = "Student"
}

The variable can then be used with:

var.student_name

Example:

content = "Student Name: ${var.student_name}"
9. Terraform Outputs

Outputs display useful information after Terraform operations.

Example:

output "profile_file" {
  value = local_file.profile.filename
}

Another example:

output "summary_file" {
  value = local_file.summary.filename
}

After applying the configuration, the values can be displayed using:

terraform output
10. Terraform Expressions

Terraform supports expressions for referencing variables and resources.

Example:

${var.student_name}

references a variable.

A resource attribute can be referenced as:

local_file.profile.filename

This allows one resource to use information produced by another resource.

11. Terraform Dependencies

Terraform automatically creates dependencies when one resource references another resource.

For example:

content = "Profile file created: ${local_file.profile.filename}"

The summary resource depends on the profile resource because it references:

local_file.profile.filename

Terraform therefore knows that the profile file must be created before the summary file.

12. Explicit Dependencies with depends_on

Terraform also supports explicit dependencies.

Example:

resource "local_file" "summary" {
  filename = "deployment_summary.txt"

  content = "Profile file created: ${local_file.profile.filename}"

  depends_on = [local_file.profile]
}

The:

depends_on

argument explicitly tells Terraform that the summary resource depends on the profile resource.

13. Terraform Initialization

Before using most Terraform commands, the working directory must be initialized.

Command:

terraform init

Terraform initialization performs tasks such as:

Initializing the working directory
Downloading required providers
Preparing the backend
Preparing the Terraform environment

Example:

terraform init

Successful initialization generally produces output indicating that Terraform has been initialized.

14. Terraform Format

Terraform provides a formatting command.

terraform fmt

This formats Terraform configuration files according to Terraform's standard formatting style.

Example:

terraform fmt
15. Terraform Validate

The terraform validate command checks whether the configuration is syntactically and structurally valid.

Command:

terraform validate

A valid configuration can produce:

Success! The configuration is valid.

If the required provider has not been installed, Terraform may report:

Missing required provider

In that situation, initialize the directory:

terraform init

and then run:

terraform validate
16. Terraform Plan

The terraform plan command shows what Terraform intends to do.

Command:

terraform plan

Example:

Plan: 2 to add, 0 to change, 0 to destroy.

The plan allows changes to be reviewed before applying them.

17. Terraform Apply

The terraform apply command applies the configuration.

Command:

terraform apply

Terraform displays the proposed actions and asks for confirmation.

Example:

Do you want to perform these actions?
Terraform will perform the actions described above.
Only 'yes' will be accepted to approve.

Enter a value:

Enter:

yes

Terraform then creates or modifies the required resources.

Example output:

local_file.profile: Creating...
local_file.profile: Creation complete

local_file.summary: Creating...
local_file.summary: Creation complete

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
18. Terraform Output

After applying the configuration, outputs can be displayed with:

terraform output

Example:

profile_file = "student_profile.txt"
summary_file = "deployment_summary.txt"

A specific output can also be requested:

terraform output profile_file
19. Terraform State

Terraform state is used to keep track of resources managed by Terraform.

The default local state file is:

terraform.tfstate

Terraform uses the state to understand the relationship between the configuration and the resources it manages.