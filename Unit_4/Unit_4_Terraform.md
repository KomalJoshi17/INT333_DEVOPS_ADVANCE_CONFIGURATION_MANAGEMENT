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

20. terraform.tfstate

After applying the configuration, a Terraform project may contain:

terraform.tfstate

The state contains information Terraform uses to track managed resources.

Example project:

terraform-state-demo/
│
├── main.tf
├── .terraform/
├── .terraform.lock.hcl
└── terraform.tfstate
21. Viewing Terraform State

Terraform provides commands for inspecting state.

List resources:

terraform state list

Show information about a specific resource:

terraform state show local_file.profile

The state can also be examined directly as a JSON file.

On Linux:

cat terraform.tfstate

On PowerShell:

Get-Content .\terraform.tfstate
Important

Get-Content is a PowerShell command.

If you are using Linux/Ubuntu, use:

cat terraform.tfstate

instead.

22. Terraform State Commands
List Resources
terraform state list

Example:

local_file.profile
local_file.summary
Show a Resource
terraform state show local_file.profile
Pull Current State
terraform state pull
23. Terraform Destroy

The terraform destroy command removes resources managed by Terraform.

Command:

terraform destroy

Terraform displays the resources that will be removed and asks for confirmation.

Enter:

yes

Terraform then destroys the resources.

Example:

Destroy complete! Resources: 2 destroyed.
24. Terraform Workflow

The commonly used Terraform workflow is:

Write Configuration
        ↓
terraform init
        ↓
terraform fmt
        ↓
terraform validate
        ↓
terraform plan
        ↓
terraform apply
        ↓
terraform output
        ↓
terraform state
        ↓
terraform destroy

25. Practical Experiment — Terraform State Demo
Objective

To create and manage local files using Terraform and understand Terraform state, resources, variables, outputs, and dependencies.

25.1 Create the Project Directory

Example:

mkdir -p ~/terraform-lab/terraform-state-demo

Move into the directory:

cd ~/terraform-lab/terraform-state-demo
26. Create main.tf

Create the Terraform configuration:

nano main.tf

Use the following configuration:

terraform {
  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "~> 2.5"
    }
  }
}

provider "local" {}

variable "student_name" {
  default = "Student"
}

resource "local_file" "profile" {
  filename = "student_profile.txt"

  content = "Student Name: ${var.student_name}\nThis profile is created by Terraform."
}

resource "local_file" "summary" {
  filename = "deployment_summary.txt"

  content = "Profile file created: ${local_file.profile.filename}\nProfile ID: ${local_file.profile.id}"

  depends_on = [local_file.profile]
}

output "profile_file" {
  value = local_file.profile.filename
}

output "summary_file" {
  value = local_file.summary.filename
}
27. Initialize the Terraform Project

Run:

terraform init

Terraform downloads the Local provider.

A successful initialization allows subsequent Terraform commands to use the provider.

28. Format the Configuration

Run:

terraform fmt

This formats the configuration.

29. Validate the Configuration

Run:

terraform validate

Expected result:

Success! The configuration is valid.
30. Generate Terraform Plan

Run:

terraform plan

Terraform displays the resources that it plans to create.

Expected result for the experiment:

Plan: 2 to add, 0 to change, 0 to destroy.

The two resources are:

local_file.profile
local_file.summary
31. Apply the Configuration

Run:

terraform apply

When prompted:

Do you want to perform these actions?

Enter:

yes

Terraform creates:

student_profile.txt
deployment_summary.txt

Expected result:

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
32. Check Terraform Outputs

Run:

terraform output

Expected result:

profile_file = "student_profile.txt"
summary_file = "deployment_summary.txt"
33. Check Created Files

Run:

ls

Expected files include:

main.tf
student_profile.txt
deployment_summary.txt
terraform.tfstate

Additional Terraform files/directories may also be present.

34. View Student Profile

Run:

cat student_profile.txt

Expected content:

Student Name: Student
This profile is created by Terraform.
35. View Deployment Summary

Run:

cat deployment_summary.txt

The file contains information about the profile file and its Terraform-generated resource ID.

Example structure:

Profile file created: student_profile.txt
Profile ID: <resource-id>
36. Inspect Terraform State

Run:

terraform state list

Expected result:

local_file.profile
local_file.summary
37. Inspect Profile Resource

Run:

terraform state show local_file.profile

Terraform displays details about the managed local file.

Information can include:

Filename
File permissions
File content
File ID
Hash values
38. Inspect Summary Resource

Run:

terraform state show local_file.summary

This displays information about the summary file managed by Terraform.

39. View State File on Linux

Because the experiment was performed on Linux/Ubuntu, use:

cat terraform.tfstate

Do not use:

Get-Content .\terraform.tfstate

because Get-Content is a PowerShell command.

40. Terraform State Experiment Flow
                    main.tf
                       |
                       v
                terraform init
                       |
                       v
                Provider Installed
                       |
                       v
                terraform fmt
                       |
                       v
              terraform validate
                       |
                       v
                terraform plan
                       |
                       v
               terraform apply
                       |
              +--------+--------+
              |                 |
              v                 v
       student_profile.txt  deployment_summary.txt
              |                 |
              +--------+--------+
                       |
                       v
                terraform.tfstate
                       |
                       v
                terraform output
41. Understanding the Experiment

The experiment contains two Terraform resources.

Resource 1
resource "local_file" "profile"

This creates:

student_profile.txt
Resource 2
resource "local_file" "summary"

This creates:

deployment_summary.txt

The summary resource references:

local_file.profile.filename

and:

local_file.profile.id

Therefore, Terraform knows the relationship between the two resources.

42. Resource Dependency

The experiment also contains:

depends_on = [local_file.profile]

This explicitly establishes that:

local_file.summary

depends on:

local_file.profile

Therefore:

local_file.profile
        ↓
local_file.summary
43. Terraform Commands — Quick Reference
Command	Purpose
terraform init	Initialize Terraform project
terraform fmt	Format configuration
terraform validate	Validate configuration
terraform plan	Preview changes
terraform apply	Apply configuration
terraform output	Display outputs
terraform state list	List managed resources
terraform state show	Show resource state
terraform state pull	Read current state
terraform destroy	Destroy managed resources

44. Common Errors
Error 1 — Incorrect Required Provider Block

Incorrect:

requiredproviders {

Correct:

required_providers {
Error 2 — Missing Provider

If Terraform reports:

Missing required provider

Run:

terraform init

Then:

terraform validate
Error 3 — Get-Content Not Found

If Linux shows:

Get-Content: command not found

Use:

cat terraform.tfstate

Get-Content belongs to PowerShell.

45. Important Terraform Concepts
Provider

A provider allows Terraform to communicate with a platform or service.

Example:

hashicorp/local
Resource

A resource represents something Terraform creates or manages.

Example:

local_file.profile
Variable

A variable allows reusable input values.

Example:

variable "student_name" {
  default = "Student"
}
Output

An output displays useful information after Terraform operations.

Example:

output "profile_file" {
  value = local_file.profile.filename
}
State

Terraform state records information about resources managed by Terraform.

Default state file:

terraform.tfstate
Dependency

Dependencies determine the order in which resources are created or changed.

Example:

depends_on = [local_file.profile]
46. Practical Result

After running:

terraform apply

the experiment creates two files:

student_profile.txt
deployment_summary.txt

Terraform also maintains state information:

terraform.tfstate

Outputs can be viewed using:

terraform output

Expected output:

profile_file = "student_profile.txt"
summary_file = "deployment_summary.txt"
47. Exam-Oriented Questions
Short Questions
Q1. What is Terraform?

Terraform is an Infrastructure as Code tool used to define and manage infrastructure using configuration files.

Q2. What is a provider?

A provider is a plugin that allows Terraform to interact with a particular platform or service.

Q3. What is a resource?

A resource represents an infrastructure object managed by Terraform.

Q4. What is Terraform state?

Terraform state contains information Terraform uses to track resources managed by the configuration.

Q5. What is terraform init?

It initializes a Terraform working directory and installs required providers.

Q6. What is terraform plan?

It displays the changes Terraform intends to make without applying them.

Q7. What is terraform apply?

It applies the Terraform configuration and creates or modifies resources.

Q8. What is terraform destroy?

It removes resources managed by Terraform.

Q9. What is depends_on?

depends_on explicitly defines a dependency between Terraform resources.

Q10. What is terraform.tfstate?

It is the default local Terraform state file used to track managed resources.

48. Important Practical Commands
# Create project directory
mkdir -p ~/terraform-lab/terraform-state-demo

# Enter project
cd ~/terraform-lab/terraform-state-demo

# Initialize
terraform init

# Format
terraform fmt

# Validate
terraform validate

# Preview changes
terraform plan

# Apply configuration
terraform apply

# Display outputs
terraform output

# List resources
terraform state list

# Show resource details
terraform state show local_file.profile

# View state on Linux
cat terraform.tfstate

# Destroy resources
terraform destroy
49. Quick Revision
Terraform
    ↓
Infrastructure as Code
    ↓
Configuration Files (.tf)
    ↓
Provider
    ↓
Resources
    ↓
Variables + Outputs
    ↓
terraform init
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
terraform apply
    ↓
Terraform State
    ↓
terraform output
    ↓
terraform destroy
50. Key Exam Points
Terraform is an Infrastructure as Code tool.
Terraform configurations are commonly written in HCL.
Terraform configuration files normally use the .tf extension.
Providers allow Terraform to interact with external platforms and services.
Resources represent objects managed by Terraform.
Variables allow values to be passed into Terraform configurations.
Outputs display useful information after Terraform operations.
terraform init initializes the working directory and installs required providers.
terraform fmt formats Terraform configuration files.
terraform validate validates Terraform configuration.
terraform plan previews infrastructure changes.
terraform apply applies the configuration.
terraform destroy removes managed resources.
terraform.tfstate is the default local state file.
terraform state list lists resources stored in the state.
terraform state show displays details about a resource.
depends_on can be used to explicitly define resource dependencies.
Terraform automatically detects dependencies when resources reference one another.
The Local provider can be used to create and manage local files.
The practical experiment creates student_profile.txt and deployment_summary.txt.
Conclusion

Terraform provides a structured way to define and manage infrastructure using code. Its workflow consists of writing configuration, initializing the project, formatting and validating the configuration, planning changes, applying those changes, and maintaining state.

The practical experiment demonstrates these concepts using the Local provider. Two local files are created through Terraform, outputs are displayed, dependencies are defined, and Terraform state is inspected using Terraform commands.