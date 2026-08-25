
# Unit I — Puppet Basics
## Complete Study + Practical README

> This README is designed as a single study reference for **Unit I: Puppet Basics**. It combines the syllabus topics, simple explanations, architecture, installation, practical learning, commands, revision notes, and viva preparation.

---

# 1. Unit I Syllabus

## Puppet Basics

1. Configuration Management System
   - Configuration Management
   - Pull Model
   - Push Model
2. Introduction to Puppet
   - What is Puppet?
   - Why Puppet?
   - Components of Puppet
3. Puppet Architecture
   - Puppet Master
   - Manifest
   - Template
   - Files
   - Certificate Authority
   - Puppet Client
   - Agent
   - Facter
4. Installation of Puppet
5. Puppet Development in Isolation

---

# 2. Configuration Management

## What is Configuration Management?

Configuration Management is the process of maintaining the desired configuration of systems in a consistent and controlled way.

It can be used to manage things such as:

- Packages
- Configuration files
- Services
- Users
- System settings

### Simple example

Suppose 10 servers need:

```text
Apache installed
Apache configuration present
Apache service running
```

Instead of configuring every server manually, a Configuration Management tool can define the desired state and apply it consistently.

---

# 3. Pull Model vs Push Model

Configuration Management commonly uses two approaches:

## Pull Model

In the Pull model, the client/agent contacts the central server and pulls its configuration.

```text
        Puppet Master
              ↑
              |
        pulls configuration
              |
          Puppet Agent
```

### Memory trick

```text
PULL = Client asks for configuration
```

---

## Push Model

In the Push model, the central server/controller sends configuration to the target machines.

```text
       Central Server
              |
              | pushes configuration
              ↓
          Target Machine
```

### Memory trick

```text
PUSH = Server sends configuration
```

---

# 4. Introduction to Puppet

## What is Puppet?

Puppet is a **configuration management and automation tool**.

Puppet allows administrators/developers to describe the desired state of a system using Puppet code and then apply that configuration.

### Simple definition for viva

> Puppet is a configuration management tool used to automate and maintain the desired configuration of systems.

---

# 5. Why Puppet?

Puppet is useful because it helps:

- Automate configuration.
- Maintain consistency across machines.
- Reduce manual configuration.
- Define infrastructure/configuration as code.
- Repeatedly enforce the desired state.
- Manage packages, files, services, and other system resources.

### Example

Instead of manually installing a package:

```bash
sudo apt install nginx
```

Puppet can define the desired state:

```puppet
package { 'nginx':
  ensure => installed,
}
```

The Puppet configuration describes **what state is required**, rather than requiring every manual command to be executed repeatedly.

---

# 6. Important Puppet Components

The major terms in this unit are:

```text
Puppet Master
Puppet Agent
Manifest
Template
Files
Certificate Authority (CA)
Facter
```

### Easy memory

```text
Master   → Central Puppet server
Agent    → Client machine
Manifest → Puppet configuration/code
Template → Dynamic configuration file
Files    → Static files distributed by Puppet
CA       → Certificates/trust
Facter   → Machine/system facts
```

---

# 7. Puppet Architecture

A simplified Puppet architecture:

```text
                    Puppet Master
                         |
          +--------------+--------------+
          |              |              |
        Manifest       Template         Files
          |
          |
     Certificate Authority
          |
          ↓
     Puppet Agent
          |
        Facter
          |
          ↓
      Target System
```

The exact architecture can be understood through the role of each component.

---

# 8. Puppet Master

The Puppet Master is the central Puppet server responsible for serving Puppet configuration to agents.

It can provide:

- Catalog/configuration
- Manifests
- Templates
- Files
- Certificate-related services

### Memory trick

```text
MASTER = Central controller/server
```

---

# 9. Puppet Agent

The Puppet Agent runs on a managed/client machine.

The agent:

1. Collects system information.
2. Communicates with the Puppet Master in a master-agent setup.
3. Receives/applies the desired configuration.

### Memory trick

```text
AGENT = Managed client
```

---

# 10. Manifest

A **Manifest** contains Puppet configuration/code.

Puppet manifests normally use:

```text
.pp
```

file extension.

Example:

```puppet
file { '/tmp/hello.txt':
  ensure  => file,
  content => 'Hello from Puppet!',
}
```

The manifest describes the desired state of the file.

### Memory trick

```text
Manifest = Puppet configuration/code
```

---

# 11. Template

A Puppet template is used to generate configuration files dynamically.

Templates are useful when the final file content depends on variables or system information.

A common Puppet template format is:

```text
.erb
```

Conceptually:

```text
Puppet variables/facts
        ↓
     Template
        ↓
Generated configuration file
```

### Memory trick

```text
Template = Dynamic configuration content
```

---

# 12. Files

Puppet can distribute/manage static files on target systems.

A Puppet file resource can ensure that a file exists and can manage properties such as its content, permissions, and ownership.

Example:

```puppet
file { '/tmp/example.txt':
  ensure  => file,
  content => 'Managed by Puppet',
}
```

### Memory trick

```text
Files = Static files managed/distributed by Puppet
```

---

# 13. Certificate Authority (CA)

The Certificate Authority is responsible for certificate-based trust in Puppet's master-agent architecture.

Certificates help establish trusted communication between Puppet components.

### Simple understanding

```text
Puppet Agent
     ↓
Certificate request/trust
     ↓
Puppet CA / Master
     ↓
Trusted communication
```

### Memory trick

```text
CA = Trust
```

---

# 14. Facter

Facter is used to collect information, called **facts**, about a machine.

Examples of system information/facts can include:

```text
Operating system
Hostname
IP/network information
Architecture
Memory-related information
```

Puppet can use these facts when determining configuration.

### Memory trick

```text
Facter = Facts about the machine
```

---

# 15. Desired State Concept

One of the most important Puppet ideas is **desired state**.

Instead of repeatedly giving manual commands, define the state you want.

Example:

```puppet
package { 'nginx':
  ensure => installed,
}
```

Meaning:

```text
Desired state:
nginx package → installed
```

Puppet works toward maintaining that desired state.

---

# 16. Puppet Resources

Puppet manages system objects using resources.

Common examples include:

```text
package
file
service
user
```

Example:

```puppet
file { '/tmp/demo.txt':
  ensure  => file,
  content => 'Hello Puppet',
}
```

Here:

```text
file = resource type
/tmp/demo.txt = resource title
```

---

# 17. Puppet Development in Isolation

**Development in Isolation** means testing/developing Puppet configurations locally without requiring a full Puppet Master-Agent environment.

The command commonly used for local execution is:

```bash
puppet apply
```

Example:

```bash
puppet apply example.pp
```

This applies the Puppet manifest locally.

### Memory trick

```text
puppet apply = Test/apply Puppet code locally
```

This is especially useful while learning Puppet and developing manifests.

---

# 18. Puppet Installation

After installation, verify Puppet using:

```bash
puppet --version
```

A successful installation should return the installed Puppet version.

Other useful checks:

```bash
which puppet
```

and:

```bash
puppet --help
```

---

# 19. Basic Puppet Command

The main local-development command used in this unit is:

```bash
puppet apply filename.pp
```

Example:

```bash
puppet apply hello.pp
```

This reads the Puppet manifest and applies its desired state to the local machine.

---

# 20. Practical Learning Pattern

For local Puppet development, use this workflow:

```text
Create .pp file
      ↓
Write Puppet resource
      ↓
puppet apply file.pp
      ↓
Check output
      ↓
Verify the managed resource
```

Example:

```bash
nano hello.pp
```

Put:

```puppet
file { '/tmp/hello.txt':
  ensure  => file,
  content => 'Hello from Puppet!',
}
```

Apply:

```bash
puppet apply hello.pp
```

Verify:

```bash
cat /tmp/hello.txt
```

Expected:

```text
Hello from Puppet!
```

---

# 21. Useful Puppet Resource Examples

## File

```puppet
file { '/tmp/example.txt':
  ensure  => file,
  content => 'Managed by Puppet',
}
```

## Package

```puppet
package { 'nginx':
  ensure => installed,
}
```

## Service

```puppet
service { 'nginx':
  ensure => running,
}
```

> The exact package/service names and available behavior depend on the operating system and installed software.

---

# 22. Puppet Idempotency

A key configuration-management idea is **idempotency**.

It means that repeatedly applying the same desired configuration should not unnecessarily change a system that is already in the desired state.

Example:

```puppet
file { '/tmp/demo.txt':
  ensure  => file,
  content => 'Hello',
}
```

After the file reaches the desired state, applying the manifest again should recognize that no further change is necessary.

### Memory trick

```text
Idempotent = Same desired state → no unnecessary repeated change
```

---

# 23. Important Commands Cheat Sheet

```bash
# Check Puppet version
puppet --version

# Locate Puppet
which puppet

# Show Puppet help
puppet --help

# Apply a manifest locally
puppet apply file.pp
```

For verification:

```bash
cat /tmp/file.txt
ls -l /tmp/file.txt
```

---

# 24. Important Terms — One-Line Revision

```text
Configuration Management
→ Maintaining system configuration consistently

Pull Model
→ Client pulls configuration

Push Model
→ Server pushes configuration

Puppet
→ Configuration management/automation tool

Puppet Master
→ Central Puppet server

Puppet Agent
→ Managed client

Manifest
→ Puppet configuration/code

Template
→ Dynamic configuration-file generation

Files
→ Static files managed/distributed by Puppet

CA
→ Certificate/trust authority

Facter
→ Collects machine facts

puppet apply
→ Applies Puppet manifest locally

Desired State
→ State the system should have

Idempotency
→ Repeated application avoids unnecessary changes
```

---

# 25. Viva Questions and Answers

### Q1. What is Configuration Management?

Configuration Management is the process of maintaining and controlling the desired configuration of systems consistently.

### Q2. What is the Pull model?

In the Pull model, the client/agent requests or pulls configuration from a central server.

### Q3. What is the Push model?

In the Push model, the central server sends configuration to target machines.

### Q4. What is Puppet?

Puppet is a configuration management and automation tool.

### Q5. Why is Puppet used?

Puppet is used to automate configuration, reduce manual work, maintain consistency, and enforce desired system states.

### Q6. What is a Puppet Master?

The Puppet Master is the central Puppet server that provides configuration to agents in a master-agent architecture.

### Q7. What is a Puppet Agent?

The Puppet Agent is the client component running on a managed machine.

### Q8. What is a Manifest?

A Manifest contains Puppet configuration/code and normally uses the `.pp` extension.

### Q9. What is a Template?

A Template is used to generate dynamic configuration-file content.

### Q10. What is Facter?

Facter collects facts/information about the machine, which Puppet can use during configuration.

### Q11. What is a Certificate Authority in Puppet?

The CA provides certificate-based trust for Puppet communication.

### Q12. What is `puppet apply`?

`puppet apply` applies a Puppet manifest locally, making it useful for isolated development and testing.

### Q13. What is desired state?

Desired state is the configuration condition that we want the system to maintain.

### Q14. What is idempotency?

Idempotency means repeatedly applying the same configuration does not cause unnecessary changes once the desired state has been reached.

---

# 26. Quick Architecture Revision

Remember the flow:

```text
             PUPPET MASTER
                  |
       +----------+----------+
       |          |          |
    Manifest   Template    Files
       |
       ↓
      CA / Trust
       |
       ↓
   PUPPET AGENT
       |
    Facter
       |
       ↓
  Managed Machine
```

### Super-short memory:

```text
Master → Gives configuration
Agent  → Applies/manages configuration
Manifest → Configuration code
Template → Dynamic content
Files → Static content
CA → Trust
Facter → Machine facts
```

---

# 27. Unit I Study Checklist

Use this before an exam/practical:

- [ ] Explain Configuration Management
- [ ] Explain Pull model
- [ ] Explain Push model
- [ ] Define Puppet
- [ ] Explain why Puppet is used
- [ ] Name Puppet components
- [ ] Explain Puppet Master
- [ ] Explain Puppet Agent
- [ ] Explain Manifest
- [ ] Explain Template
- [ ] Explain Files
- [ ] Explain Certificate Authority
- [ ] Explain Facter
- [ ] Explain desired state
- [ ] Explain `puppet apply`
- [ ] Explain Puppet development in isolation
- [ ] Know basic Puppet commands
- [ ] Understand idempotency

---

# 28. Unit I Completion Map

| Topic | Coverage |
|---|---|
| Configuration Management | ✅ |
| Pull Model | ✅ |
| Push Model | ✅ |
| Introduction to Puppet | ✅ |
| Why Puppet | ✅ |
| Components of Puppet | ✅ |
| Puppet Architecture | ✅ |
| Puppet Master | ✅ |
| Manifest | ✅ |
| Template | ✅ |
| Files | ✅ |
| Certificate Authority | ✅ |
| Puppet Client | ✅ |
| Agent | ✅ |
| Facter | ✅ |
| Installation of Puppet | ✅ |
| Puppet Development in Isolation | ✅ |

---

# 29. Final Memory Map

```text
                 PUPPET
                    |
        Configuration Management
                    |
          +---------+---------+
          |                   |
        PULL                 PUSH
          |
      Puppet Agent
          |
      Puppet Master
          |
   +------+------+------+
   |      |      |      |
Manifest Template Files CA
          |
        Facter
          |
     Machine Facts
          |
     Desired State
          |
     puppet apply
          |
     Managed System
```

## The 7 words to remember

```text
MASTER
AGENT
MANIFEST
TEMPLATE
FILES
CA
FACTER
```

### Final shortcut

```text
Master → Server
Agent → Client
Manifest → Code
Template → Dynamic
Files → Static
CA → Trust
Facter → Facts
```

---

# 30. What Comes After Unit I

Unit I focuses on **Puppet Basics**. The complete course continues with the following units.

## Unit II — Advanced Puppet

- Puppet Configuration
- Managing Packages in Puppet
- Puppet Modules
- Monitoring Web Servers
- Load Balancing Clusters
- Scaling Puppet Environment
- Connecting Puppet Agent with Puppet Master
- Making Configuration Dynamic
- Extending Puppet
- Puppet Classes and Functions
- Custom Functions
- Using Puppet via Command Line
- Managing Resources with `puppet apply` Command
- Puppet Manifests

## Unit III — Nagios Monitoring

- Continuous Monitoring Concepts
- Definition, Importance, and Benefits
- Introduction to Nagios
- Features and Architecture
- Nagios Plugins
- Soft and Hard States
- Installation using `apt-get/dpkg` and `yum/rpm`
- Installing Prerequisites
- Compiling and Installing Nagios
- Setting up Web Server
- Command-Line Interfaces
- Nagios Configuration
- Monitoring Web Servers
- Built-in Web Interface
- Managing Hosts, Services, Downtimes, Comments, and Information
- Deploying a Simple Web Application on Server

## Unit IV — Infrastructure as Code with Terraform

- Introduction to Terraform
- What is Terraform
- Benefits and Use Cases
- Terraform Architecture and Workflow
- Installing and Setting Up Terraform
- Writing and Managing Terraform Configuration Files
- Provisioning Infrastructure on AWS/Azure/GCP
- Managing Resources and Dependencies
- Terraform State and State Management
- Modules and Workspaces
- Terraform with Ansible for Advanced Automation
- Best Practices and Security Considerations

## Unit V — Introduction to Ansible

- Introduction to Ansible and Configuration Management
- How Ansible Works
- Modern Infrastructure Management
- Ansible and RedHat
- Ansible Architecture
- Infrastructure Management: From Shell Scripts to Ansible
- Installing Ansible
- Creating a Basic Inventory File
- Using Ansible with Vagrant

## Unit VI — Advanced Ansible

- Ansible Roles and Command Line Usage
- Playbooks
- Playbook Structure
- Writing and Running Playbooks with `ansible-playbook`
- Real-world Playbook Examples
- Handlers
- Environment Variables
- Variables
- Facts
- Prompts
- Tags
- Blocks
- Ansible with AWS for Application Deployment

> **Note:** This section is only a roadmap of what comes after Unit I. The actual study content in this README remains focused on **Unit I — Puppet Basics**.

Keep this README as the main **Unit I study and revision document**.