# Unit II — Advanced Puppet




> **Course:** INT333 — DevOps / Advanced Configuration Management  
> **Unit:** II  
> **Topic:** Advanced Puppet  
> **Purpose:** Complete study, practical, revision, and viva reference

---

# 1. Unit II Syllabus

This unit covers:

1. Puppet Configuration
2. Managing Packages in Puppet
3. Puppet Modules
4. Monitoring Web Servers
5. Load Balancing Clusters
6. Scaling Puppet Environment
7. Connecting Puppet Agent with Puppet Master
8. Making Configuration Dynamic
9. Extending Puppet
10. Puppet Classes and Functions
11. Custom Functions
12. Using Puppet via Command Line
13. Managing Resources with `puppet apply` Command
14. Puppet Manifests

---

# 2. Puppet Configuration

Puppet is a configuration management tool used to define and maintain the desired state of systems.

Puppet configurations are normally written in Puppet manifests using `.pp` files.

Example:

```puppet
file { '/tmp/hello.txt':
  ensure  => file,
  content => 'Hello from Puppet!',
}
```

This configuration tells Puppet to:

- Create the file if it does not exist.
- Ensure it remains a file.
- Maintain the specified content.

Apply the manifest:

```bash
puppet apply hello.pp
```

Verify:

```bash
cat /tmp/hello.txt
```

---

# 3. Puppet Resources

A resource represents something that Puppet manages.

Common resource types:

| Resource | Purpose |
|---|---|
| `file` | Manage files and directories |
| `package` | Install/remove packages |
| `service` | Manage services |
| `user` | Manage users |
| `group` | Manage groups |
| `exec` | Execute commands |
| `cron` | Manage scheduled jobs |

Example:

```puppet
file { '/tmp/example.txt':
  ensure  => file,
  content => 'Managed by Puppet',
}
```

Important terms:

- **Resource type** → `file`
- **Resource title** → `/tmp/example.txt`
- **Attribute** → `ensure`, `content`
- **Desired state** → What the resource should look like

---

# 4. Managing Packages in Puppet

Puppet can manage software packages.

Basic example:

```puppet
package { 'nginx':
  ensure => installed,
}
```

Apply:

```bash
puppet apply nginx.pp
```

Puppet checks the current state and makes the required change.

## Package States

### Install a package

```puppet
package { 'nginx':
  ensure => installed,
}
```

### Remove a package

```puppet
package { 'nginx':
  ensure => absent,
}
```

### Install the latest available version

```puppet
package { 'nginx':
  ensure => latest,
}
```

---

# 5. Managing Services

Puppet can also manage services.

Example:

```puppet
service { 'nginx':
  ensure => running,
}
```

To ensure that the service starts automatically:

```puppet
service { 'nginx':
  ensure => running,
  enable => true,
}
```

---

# 6. Package and Service Together

A common configuration is:

```puppet
package { 'nginx':
  ensure => installed,
}

service { 'nginx':
  ensure => running,
  enable => true,
}
```

This ensures:

1. Nginx is installed.
2. Nginx is running.
3. Nginx is enabled.

A dependency can also be specified:

```puppet
package { 'nginx':
  ensure => installed,
}

service { 'nginx':
  ensure  => running,
  enable  => true,
  require => Package['nginx'],
}
```

The dependency is:

```text
Package
   |
   v
Service
```

---

# 7. Puppet Modules

A Puppet module is a reusable structure for organizing Puppet code.

Modules make configurations:

- Reusable
- Organized
- Easier to maintain
- Easier to distribute
- Easier to scale

A simple module structure is:

```text
mymodule/
├── manifests/
│   └── init.pp
├── files/
├── templates/
├── lib/
└── metadata.json
```

## Important Module Directories

| Directory | Purpose |
|---|---|
| `manifests/` | Puppet manifests |
| `files/` | Static files |
| `templates/` | Dynamic templates |
| `lib/` | Supporting code |
| `metadata.json` | Module metadata |

---

# 8. Creating a Simple Puppet Module

Create the directory:

```bash
mkdir -p mymodule/manifests
```

Create the main manifest:

```bash
nano mymodule/manifests/init.pp
```

Add:

```puppet
class mymodule {
  file { '/tmp/module.txt':
    ensure  => file,
    content => 'Managed by Puppet Module',
  }
}
```

The module contains a class named:

```text
mymodule
```

It manages:

```text
/tmp/module.txt
```

---

# 9. Using a Puppet Module Class

A class can be included from a manifest:

```puppet
include mymodule
```

Example:

```puppet
class mymodule {
  file { '/tmp/module.txt':
    ensure  => file,
    content => 'Managed by Puppet Module',
  }
}

include mymodule
```

Apply the manifest:

```bash
puppet apply site.pp
```

---

# 10. Static Files in Puppet Modules

Files can be stored inside the module's `files/` directory.

Example:

```text
mymodule/
├── manifests/
│   └── init.pp
└── files/
    └── example.txt
```

The file can be managed using:

```puppet
file { '/tmp/example.txt':
  ensure => file,
  source => 'puppet:///modules/mymodule/example.txt',
}
```

Here:

```text
puppet:///modules/mymodule/example.txt
```

refers to the file stored in the module.

---

# 11. Templates

Templates are useful when configuration needs dynamic values.

Example:

```text
mymodule/
├── manifests/
│   └── init.pp
└── templates/
    └── example.conf.erb
```

A template can contain dynamic values.

Example:

```erb
server_name <%= @hostname %>;
```

The template can then be managed using:

```puppet
file { '/tmp/example.conf':
  ensure  => file,
  content => template('mymodule/example.conf.erb'),
}
```

---

# 12. Monitoring Web Servers with Puppet

Puppet can help maintain web server configuration.

For example, Puppet can ensure:

- Web server package is installed.
- Service is running.
- Service is enabled.
- Configuration files exist.
- Required files are deployed.

Example:

```puppet
package { 'nginx':
  ensure => installed,
}

service { 'nginx':
  ensure => running,
  enable => true,
}
```

Puppet is primarily responsible for maintaining the desired configuration state.

---

# 13. Web Server Configuration File

Puppet can manage a web server configuration file.

Example:

```puppet
file { '/etc/nginx/nginx.conf':
  ensure => file,
  source => 'puppet:///modules/nginx/nginx.conf',
}
```

This allows the configuration to be maintained centrally through Puppet.

---

# 14. Load Balancing Clusters

Load balancing distributes incoming requests among multiple servers.

Basic architecture:

```text
                  Users
                    |
                    v
             +-------------+
             |Load Balancer|
             +-------------+
               /    |    \
              /     |     \
             v      v      v
          Server1 Server2 Server3
```

Benefits include:

- Better availability
- Distribution of traffic
- Better utilization of servers
- Improved scalability

Puppet can be used to maintain consistent configurations on the servers in the cluster.

---

# 15. Puppet in a Cluster

Suppose three web servers need the same configuration:

```text
Web Server 1
Web Server 2
Web Server 3
```

Instead of configuring each machine manually, Puppet can apply a common configuration.

For example:

```puppet
package { 'nginx':
  ensure => installed,
}

service { 'nginx':
  ensure => running,
  enable => true,
}
```

This helps maintain consistency across the cluster.

---

# 16. Scaling a Puppet Environment

Scaling means managing a growing number of machines.

Without configuration management:

```text
Server 1 → Manual configuration
Server 2 → Manual configuration
Server 3 → Manual configuration
...
Server 100 → Manual configuration
```

With Puppet:

```text
                 Puppet
                   |
        +----------+----------+
        |          |          |
     Server 1   Server 2   Server 3
        |          |          |
        +----------+----------+
             Common Policy
```

Advantages:

- Consistency
- Automation
- Reduced manual work
- Easier maintenance
- Faster deployment
- Centralized configuration

---

# 17. Puppet Master and Agent

The traditional Puppet architecture contains a Puppet Master and Puppet Agents.

```text
                Puppet Master
                     |
          Configuration / Catalog
                     |
        +------------+------------+
        |            |            |
        v            v            v
     Agent 1      Agent 2      Agent 3
```

### Puppet Master

The central server that manages and distributes Puppet configuration.

### Puppet Agent

The client machine that requests and applies configuration.

---

# 18. Connecting Puppet Agent with Puppet Master

The general workflow is:

```text
Puppet Agent
     |
     | Request configuration
     v
Puppet Master
     |
     | Return configuration/catalog
     v
Puppet Agent
     |
     v
Apply desired state
```

A test run can be started using:

```bash
puppet agent --test
```

This is useful for checking the agent's interaction with the Puppet environment.

---

# 19. Puppet Certificates and Trust

Puppet Master-Agent communication uses certificates for identity and trust.

Basic concept:

```text
Agent
  |
  | Certificate request
  v
Puppet Master / CA
  |
  | Certificate approval
  v
Trusted Agent
```

The Certificate Authority (CA) is responsible for certificate-related trust.

Important idea:

```text
CA → Trust → Secure Master-Agent communication
```

---

# 20. Making Configuration Dynamic

Dynamic configuration means that Puppet configuration can change according to:

- Variables
- Parameters
- Facts
- System information
- Environment-specific values

Example using a class parameter:

```puppet
class webserver(
  String $package_name = 'nginx'
) {
  package { $package_name:
    ensure => installed,
  }
}
```

The package name can be supplied dynamically.

---

# 21. Using Facter for Dynamic Configuration

Facter provides information about the system.

Run:

```bash
facter
```

Examples of information include:

- Operating system
- Hostname
- IP address
- Architecture
- Memory
- Processor information

Check a particular fact:

```bash
facter hostname
```

or:

```bash
facter os
```

---

# 22. Conditional Configuration Using Facts

Facts can be used to make configuration decisions.

Example:

```puppet
if $facts['os']['family'] == 'Debian' {
  package { 'nginx':
    ensure => installed,
  }
}
```

The configuration is evaluated based on the operating system information.

This is useful when the same Puppet code needs to behave differently on different systems.

---

# 23. Extending Puppet

Puppet can be extended when built-in functionality is not enough.

Possible extension mechanisms include:

- Custom functions
- Custom types and providers
- Modules
- Supporting Ruby code

A simplified concept:

```text
Puppet
   |
   +---- Modules
   |
   +---- Functions
   |
   +---- Custom Types
   |
   +---- Providers
```

Extensions allow Puppet to support specialized configuration requirements.

---

# 24. Puppet Classes

A class is a reusable block of Puppet code.

Example:

```puppet
class webserver {
  package { 'nginx':
    ensure => installed,
  }

  service { 'nginx':
    ensure => running,
    enable => true,
  }
}
```

Use the class:

```puppet
include webserver
```

Classes improve:

- Reusability
- Organization
- Maintainability
- Scalability

---

# 25. Puppet Functions

Functions perform operations during Puppet processing.

Example:

```puppet
$server_name = upcase('webserver')
```

Functions can be useful for:

- String manipulation
- Data transformation
- Calculations
- Working with arrays
- Conditional logic

Example:

```puppet
$name = 'puppet'
$upper_name = upcase($name)
```

---

# 26. Custom Puppet Functions

A custom function allows Puppet to be extended with user-defined functionality.

Conceptually:

```text
Puppet Manifest
      |
      v
Custom Function
      |
      v
Calculated / Processed Value
      |
      v
Puppet Configuration
```

Custom functions are useful when built-in functions do not provide the required behavior.

They are generally implemented using Puppet's supported function development mechanisms.

---

# 27. Puppet Command Line

Important Puppet commands:

### Check version

```bash
puppet --version
```

### Display help

```bash
puppet --help
```

### Apply a manifest

```bash
puppet apply example.pp
```

### Test Puppet Agent

```bash
puppet agent --test
```

### Display facts

```bash
facter
```

### Display hostname fact

```bash
facter hostname
```

### Display operating system fact

```bash
facter os
```

### Inspect package resources

```bash
puppet resource package
```

### Inspect service resources

```bash
puppet resource service
```

---

# 28. Managing Resources with puppet apply

`puppet apply` applies a Puppet manifest directly to the local machine.

Create a manifest:

```bash
nano example.pp
```

Add:

```puppet
file { '/tmp/puppet-test.txt':
  ensure  => file,
  content => 'Created using puppet apply',
}
```

Run:

```bash
puppet apply example.pp
```

Verify:

```bash
cat /tmp/puppet-test.txt
```

Expected:

```text
Created using puppet apply
```

This is particularly useful for:

- Learning Puppet
- Testing manifests
- Local development
- Troubleshooting
- Development in isolation

---

# 29. Puppet Manifests

A Puppet manifest is a file containing Puppet configuration code.

Manifest files normally use:

```text
.pp
```

Examples:

```text
site.pp
webserver.pp
example.pp
```

Example:

```puppet
file { '/tmp/example.txt':
  ensure  => file,
  content => 'Hello Puppet',
}
```

Apply it:

```bash
puppet apply example.pp
```

## Manifest Structure

A simple manifest can contain:

```text
Resource
   ↓
Resource attributes
   ↓
Desired state
```

Example:

```puppet
package { 'nginx':
  ensure => installed,
}
```

---

# 30. What Comes After Unit II

Unit II focuses on **Advanced Puppet**. The complete course continues with the following units.

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

> **Note:** This section is only a roadmap of what comes after Unit II. The actual study content in this README remains focused on **Unit II — Advanced Puppet**.

---

# Unit II — Quick Revision

## Remember

```text
Puppet Configuration
        ↓
Resources
        ↓
Packages
        ↓
Services
        ↓
Modules
        ↓
Classes
        ↓
Functions
        ↓
Custom Functions
        ↓
Facts
        ↓
Dynamic Configuration
        ↓
Master / Agent
        ↓
puppet apply
        ↓
Manifests
```

## Important Commands

```bash
puppet --version
puppet --help
puppet apply file.pp
puppet agent --test
facter
facter hostname
facter os
puppet resource package
puppet resource service
```

## Important Resource Types

```text
file
package
service
user
group
exec
cron
```

## Important Concepts

```text
Module       → Reusable Puppet structure
Class        → Reusable Puppet code
Function     → Performs an operation
Custom       → Extends Puppet functionality
Fact         → System information
Manifest     → Puppet configuration file
Resource     → Object managed by Puppet
Master       → Central Puppet server
Agent        → Managed Puppet client
puppet apply  → Applies manifest locally
```

---

# Unit II — Viva Questions

### Q1. What is Puppet?

Puppet is a configuration management tool used to automate and maintain the desired state of systems.

### Q2. What is a Puppet resource?

A resource is an object that Puppet manages, such as a file, package, or service.

### Q3. What is a Puppet manifest?

A manifest is a `.pp` file containing Puppet configuration code.

### Q4. What is a Puppet module?

A module is a reusable structure that organizes Puppet code and related files.

### Q5. What is a Puppet class?

A class is a reusable block of Puppet code.

### Q6. What is Facter?

Facter provides information about the system on which Puppet is running.

### Q7. What is `puppet apply`?

`puppet apply` applies a Puppet manifest directly to the local machine.

### Q8. What does `puppet agent --test` do?

It runs the Puppet agent in test mode and is useful for testing the agent's configuration and interaction with the Puppet environment.

### Q9. How does Puppet manage packages?

Puppet uses the `package` resource type.

Example:

```puppet
package { 'nginx':
  ensure => installed,
}
```

### Q10. How does Puppet manage services?

Using the `service` resource type.

Example:

```puppet
service { 'nginx':
  ensure => running,
}
```

### Q11. What are Puppet functions?

Functions perform operations during Puppet processing.

### Q12. What are custom functions?

Custom functions are user-defined extensions that add specialized functionality to Puppet.

### Q13. What are Puppet facts?

Facts are system information collected by Facter.

### Q14. What is dynamic configuration?

Dynamic configuration allows Puppet to make configuration decisions based on variables, parameters, facts, or other changing values.

### Q15. Why are modules useful?

Modules make Puppet code reusable, organized, maintainable, and easier to scale.

---

# Unit II — Practical Checklist

- [ ] Understand Puppet configuration
- [ ] Understand Puppet resources
- [ ] Manage packages
- [ ] Manage services
- [ ] Understand Puppet modules
- [ ] Create a basic module
- [ ] Use module files
- [ ] Understand templates
- [ ] Understand web server management
- [ ] Understand load balancing clusters
- [ ] Understand scaling
- [ ] Understand Master-Agent architecture
- [ ] Understand certificate-based trust
- [ ] Understand dynamic configuration
- [ ] Use Facter
- [ ] Understand Puppet classes
- [ ] Understand Puppet functions
- [ ] Understand custom functions
- [ ] Use Puppet CLI
- [ ] Use `puppet apply`
- [ ] Write Puppet manifests
- [ ] Understand resource dependencies
- [ ] Understand idempotent configuration

---

# Final Memory Trick

```text
MASTER → CENTRAL SERVER
AGENT → CLIENT
MANIFEST → CONFIGURATION CODE
MODULE → REUSABLE STRUCTURE
CLASS → REUSABLE CODE
FUNCTION → OPERATION
FACTER → SYSTEM FACTS
RESOURCE → MANAGED OBJECT
PACKAGE → SOFTWARE
SERVICE → RUNNING PROGRAM
puppet apply → LOCAL APPLY
```

## Unit II in One Line

> **Advanced Puppet = Configuration + Packages + Modules + Classes + Functions + Facts + Dynamic Configuration + Master-Agent + CLI + Manifests**
