# Unit I — Puppet Basics: Full-Fledged Study & Practical README

## 1. Unit Overview

This unit introduces Puppet as a Configuration Management System and covers:

1. Configuration Management System
   - Configuration Management
   - Pull
   - Push
2. Introduction to Puppet
   - Puppet
   - Why Puppet
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

# 2. Configuration Management System

## Configuration Management

Configuration Management is the process of maintaining systems in a defined, consistent, and desired configuration.

Without configuration management, systems can develop **configuration drift**: the actual system gradually differs from the intended configuration.

### Why Configuration Management?

It helps with:

- Consistency
- Automation
- Repeatability
- Reduced manual work
- Maintaining the desired configuration
- Managing multiple systems

### Memory Trick

**CM = Consistent Machines**

---

# 3. Push and Pull Models

Configuration management commonly uses two approaches.

## Push Model

In the **Push** model, a central server sends configuration changes to client machines.

```text
Central Server
      |
      | pushes configuration
      ↓
   Client
```

### Memory

**Push = Server sends**

---

## Pull Model

In the **Pull** model, the client/agent contacts the central server and retrieves its configuration.

```text
Client / Agent
      |
      | pulls configuration
      ↓
Central Server
```

### Memory

**Pull = Client asks**

---

# 4. Introduction to Puppet

## What is Puppet?

Puppet is a configuration management and automation tool used to define and maintain the desired state of systems.

A Puppet manifest describes what configuration should exist, and Puppet works to make the actual system match that desired configuration.

### Core Memory

**Puppet = Desired State + Automation**

---

# 5. Why Puppet?

Puppet is useful because it can automate management of:

- Files
- Packages
- Services
- System configuration

Instead of manually configuring systems repeatedly, the configuration can be written as Puppet code and applied consistently.

---

# 6. Components of Puppet

Important components in this unit are:

```text
Puppet Master / Server
        |
        | configuration
        ↓
Puppet Agent / Client
        |
        +---- Facter → system facts
```

Other important components:

- Manifest
- Template
- Files
- Certificate Authority

---

# 7. Puppet Architecture

## Puppet Master / Server

The Puppet Master is the central Puppet server responsible for providing configuration to Puppet clients/agents.

It can contain:

- Manifests
- Templates
- Files
- Certificate Authority

### Memory

**Master = Central configuration**

---

## Manifest

A **manifest** contains Puppet code that describes the desired configuration.

Example:

```puppet
file { '/tmp/example.txt':
  ensure  => file,
  content => 'Hello from Puppet!',
}
```

This tells Puppet that the file should exist with the specified content.

### Memory

**Manifest = What we want**

---

## Template

Templates are used to create dynamic configuration files whose contents can depend on variables or facts.

For this syllabus, remember:

**Template = Dynamic configuration content**

---

## Files

Puppet can manage files on target systems.

Example:

```puppet
file { '/tmp/example.txt':
  ensure => file,
}
```

**Files = Data/configuration managed by Puppet**

---

## Certificate Authority (CA)

The Certificate Authority is used for trust and certificate management between Puppet infrastructure components.

### Memory

**CA = Trust**

---

# 8. Puppet Client

## Agent

The Puppet Agent is the client-side component that runs on a managed machine.

It receives/uses Puppet configuration and applies the desired state to the machine.

### Memory

**Agent = Client**

---

## Facter

Facter collects information about the machine.

Examples of facts include:

- Hostname
- Operating system
- IP/network information
- Architecture
- Processor information
- Virtualization information

In the lab, Facter reported:

```text
Hostname: Komal-Joshi
OS: Ubuntu 26.04.1 LTS
Architecture: amd64
Processors: 8 cores / 16 threads
Virtualization: kvm
```

### Memory

**Facter = Facts about the machine**

---

# 9. Puppet Installation

Puppet is already installed in the lab environment.

Version checked:

```bash
puppet --version
```

Result:

```text
8.10.0
```

Therefore, no additional installation was required for the practical work.

---

# 10. Puppet Development in Isolation

## Meaning

Puppet development in isolation means writing and testing Puppet code in a local/test environment before using it in a production environment.

For this practical setup:

```text
Windows
   ↓
WSL2
   ↓
Ubuntu
   ↓
Puppet
   ↓
Local test manifests
```

The local command used to apply a manifest is:

```bash
puppet apply <manifest>.pp
```

### Development Flow

```text
Write Puppet code
       ↓
Apply locally
       ↓
Check result
       ↓
Fix/test
       ↓
Deploy when ready
```

---

# 11. Practical Lab Setup

Lab directory:

```bash
mkdir -p ~/puppet-lab
cd ~/puppet-lab
```

Check Puppet:

```bash
puppet --version
```

Check Facter:

```bash
facter
```

---

# 12. Practical 1 — File Management

Create:

```text
hello.pp
```

Code:

```puppet
file { '/tmp/puppet-hello.txt':
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
cat /tmp/puppet-hello.txt
```

Expected:

```text
Hello from Puppet!
```

## Desired State Test

Change the file manually:

```bash
echo "I changed this manually" > /tmp/puppet-hello.txt
```

Apply Puppet again:

```bash
puppet apply hello.pp
```

Verify:

```bash
cat /tmp/puppet-hello.txt
```

Puppet restores the desired content.

### Concept Demonstrated

**Desired State + Idempotency**

---

# 13. Practical 2 — Package Management

Create:

```text
package.pp
```

Code:

```puppet
package { 'apache2':
  ensure => installed,
}
```

Apply:

```bash
puppet apply package.pp
```

Verify:

```bash
apache2 -v
```

The lab already had Apache installed, so Puppet maintained the requested state.

---

# 14. Practical 3 — Service Management

Create:

```text
apache.pp
```

Code:

```puppet
package { 'apache2':
  ensure => installed,
}

service { 'apache2':
  ensure => running,
  enable => true,
}
```

Apply:

```bash
puppet apply apache.pp
```

Check:

```bash
systemctl status apache2
```

This demonstrates Puppet managing both:

- Package installation
- Service state

### WSL2 Lab Note

When Apache was manually stopped and Puppet was asked to start it, Puppet reported a Systemd start failure in this WSL2 environment. Apache itself could be started successfully using:

```bash
sudo systemctl start apache2
```

The issue was specific to service enforcement in this lab environment; the Puppet service resource concept was successfully demonstrated when Apache was already running.

---

# 15. Practical 4 — Facter

Run:

```bash
facter
```

Facter displays system facts.

Useful focused commands:

```bash
facter os
facter hostname
facter networking.ip
```

Examples from this lab:

```text
hostname => Komal-Joshi
os => Ubuntu 26.04.1 LTS
```

---

# 16. Practical 5 — Using Facter in Puppet

Create:

```text
facter.pp
```

Code:

```puppet
file { '/tmp/puppet-system-info.txt':
  ensure  => file,
  content => "Hostname: ${facts['networking']['hostname']}
OS: ${facts['os']['name']}
",
}
```

Apply:

```bash
puppet apply facter.pp
```

Verify:

```bash
cat /tmp/puppet-system-info.txt
```

Expected:

```text
Hostname: Komal-Joshi
OS: Ubuntu
```

### Concept

```text
Facter
   ↓
Collects facts
   ↓
Puppet manifest
   ↓
Uses facts
   ↓
Creates/manages configuration
```

---

# 17. Practical 6 — Development in Isolation

Create:

```text
isolation.pp
```

Code:

```puppet
file { '/tmp/isolation-test.txt':
  ensure  => file,
  content => 'Puppet development in isolation',
}
```

Apply:

```bash
puppet apply isolation.pp
```

Verify:

```bash
cat /tmp/isolation-test.txt
```

Expected:

```text
Puppet development in isolation
```

This demonstrates local Puppet development without requiring a Puppet Master.

---

# 18. Most Important Puppet Resources

| Resource | Purpose |
|---|---|
| `file` | Manage files |
| `package` | Manage software packages |
| `service` | Manage services |

Examples:

```puppet
file { '/tmp/a.txt':
  ensure => file,
}
```

```puppet
package { 'apache2':
  ensure => installed,
}
```

```puppet
service { 'apache2':
  ensure => running,
}
```

---

# 19. Most Important Commands

### Puppet version

```bash
puppet --version
```

### Apply a manifest

```bash
puppet apply filename.pp
```

### Display all facts

```bash
facter
```

### Display OS facts

```bash
facter os
```

### Display hostname

```bash
facter hostname
```

### Display network IP

```bash
facter networking.ip
```

### Check Apache

```bash
systemctl status apache2
```

### Start Apache

```bash
sudo systemctl start apache2
```

### Stop Apache

```bash
sudo systemctl stop apache2
```

---

# 20. Final Memory Map

```text
                 PUPPET
                   |
          Desired State
                   |
        ---------------------
        |         |         |
      File     Package    Service
        |
     Manifest
        |
      Facter
        |
   Machine Facts
```

Architecture memory:

```text
MASTER
  |
  +-- Manifest
  +-- Template
  +-- Files
  +-- CA
  |
  ↓
AGENT / CLIENT
  |
  +-- Facter
```

Communication memory:

```text
PUSH → Server sends
PULL → Client asks
```

Core definitions:

```text
Puppet   = Desired State + Automation
Manifest = What we want
Agent    = Client
Facter   = Machine facts
CA       = Trust
Template = Dynamic configuration
```

---

# 21. Unit I Exam/Viva One-Liners

**Q: What is Puppet?**  
A: Puppet is a configuration management and automation tool used to maintain the desired state of systems.

**Q: What is a manifest?**  
A: A manifest contains Puppet code that defines the desired configuration.

**Q: What is Facter?**  
A: Facter collects information, or facts, about a system for Puppet.

**Q: What is an agent?**  
A: The agent is the client-side Puppet component running on a managed machine.

**Q: What is a Puppet Master?**  
A: It is the central Puppet server that provides configuration to clients/agents.

**Q: What is Push?**  
A: The server sends configuration to clients.

**Q: What is Pull?**  
A: The client/agent retrieves configuration from the server.

**Q: What is Puppet development in isolation?**  
A: It is developing and testing Puppet code locally in a test environment before production use.

**Q: What does `puppet apply` do?**  
A: It applies a Puppet manifest locally.

**Q: What is idempotency?**  
A: Applying the same desired configuration repeatedly should not cause unnecessary changes.

---

## Unit I Completion Checklist

- [x] Configuration Management
- [x] Push
- [x] Pull
- [x] Introduction to Puppet
- [x] Why Puppet
- [x] Components of Puppet
- [x] Puppet Master
- [x] Manifest
- [x] Template
- [x] Files
- [x] Certificate Authority
- [x] Puppet Client
- [x] Agent
- [x] Facter
- [x] Puppet Installation
- [x] Puppet Development in Isolation
- [x] File practical
- [x] Package practical
- [x] Service practical
- [x] Facter practical
- [x] Facter + Puppet practical
- [x] Isolation practical