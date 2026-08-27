# Unit I — Puppet Basics: Experiments README


## Environment

- OS: Ubuntu 26.04.1 LTS on WSL2
- Puppet: 8.10.0
- Lab directory: `~/puppet-lab`
- Execution mode: Local Puppet development using `puppet apply`

---

## Experiment 1 — First Puppet Manifest: File Management

### Objective
Create and manage a file using Puppet.

### Manifest

File: `hello.pp`

```puppet
file { '/tmp/puppet-hello.txt':
  ensure  => file,
  content => 'Hello from Puppet!',
}
```

### Commands

```bash
cd ~/puppet-lab
nano hello.pp
puppet apply hello.pp
cat /tmp/puppet-hello.txt
```

### Expected output

```text
Hello from Puppet!
```

### Idempotency / Desired State Test

Run the manifest again:

```bash
puppet apply hello.pp
```

Then manually change the file:

```bash
echo "I changed this manually" > /tmp/puppet-hello.txt
```

Apply Puppet again:

```bash
puppet apply hello.pp
cat /tmp/puppet-hello.txt
```

The file is restored to:

```text
Hello from Puppet!
```

### Learning
Puppet enforces the **desired state**. Reapplying the same manifest does not make unnecessary changes.

---

## Experiment 2 — Package Management

### Objective
Use Puppet to ensure Apache is installed.

### Manifest

File: `package.pp`

```puppet
package { 'apache2':
  ensure => installed,
}
```

### Commands

```bash
nano package.pp
puppet apply package.pp
apache2 -v
```

### Expected result

Apache is installed and its version can be checked.

Example:

```text
Server version: Apache/2.4.66 (Ubuntu)
```

### Learning
Puppet can manage software packages and maintain their desired installation state.

---

## Experiment 3 — Service Management

### Objective
Use Puppet to ensure the Apache service is running and enabled.

### Manifest

File: `apache.pp`

```puppet
package { 'apache2':
  ensure => installed,
}

service { 'apache2':
  ensure => running,
  enable => true,
}
```

### Commands

```bash
nano apache.pp
puppet apply apache.pp
systemctl status apache2
```

### Expected result

When Apache is already running, Puppet applies the catalog successfully and Apache remains active.

### Service enforcement test

Stop Apache:

```bash
sudo systemctl stop apache2
```

Then apply Puppet:

```bash
puppet apply apache.pp
```

### WSL note

In this WSL2 lab environment, Puppet reported a Systemd start failure when it attempted to restart Apache after it had been manually stopped, even though Apache itself could be started successfully with:

```bash
sudo systemctl start apache2
```

This is an environment-specific service-management behavior. It does not invalidate the Puppet service manifest concept.

---

## Experiment 4 — Facter Information

### Objective
Collect system information using Facter.

### Command

```bash
facter
```

Facter returned information about the machine, including networking, OS, processors, Ruby, SSH, uptime, and virtualization.

Important examples from the lab:

```text
hostname => "Komal-Joshi"
OS => Ubuntu 26.04.1 LTS
architecture => amd64
processors => 8 cores / 16 threads
virtual => kvm
```

### Useful focused commands

```bash
facter os
facter hostname
facter networking.ip
```

### Learning

**Facter = Puppet's information collector.**

It collects facts about the machine so Puppet can use those facts in manifests.

---

## Experiment 5 — Using Facter in a Puppet Manifest

### Objective
Use Facter facts inside Puppet code.

### Manifest

File: `facter.pp`

```puppet
file { '/tmp/puppet-system-info.txt':
  ensure  => file,
  content => "Hostname: ${facts['networking']['hostname']}
OS: ${facts['os']['name']}
",
}
```

### Commands

```bash
nano facter.pp
puppet apply facter.pp
cat /tmp/puppet-system-info.txt
```

### Expected output

```text
Hostname: Komal-Joshi
OS: Ubuntu
```

### Learning

The flow is:

```text
Facter
   ↓
Collect machine facts
   ↓
Puppet Manifest
   ↓
Use facts
   ↓
Configure the system
```

---

## Experiment 6 — Puppet Development in Isolation

### Objective
Develop and test Puppet code locally without a Puppet Master.

### Manifest

File: `isolation.pp`

```puppet
file { '/tmp/isolation-test.txt':
  ensure  => file,
  content => 'Puppet development in isolation',
}
```

### Commands

```bash
cd ~/puppet-lab
nano isolation.pp
puppet apply isolation.pp
cat /tmp/isolation-test.txt
```

### Expected output

```text
Puppet development in isolation
```

### Learning

Development in isolation means:

```text
Write → Apply locally → Test → Fix → Deploy
```

A local WSL2 Ubuntu machine can be used as the test environment.

---

## Quick Experiment Summary

| Experiment | Main Puppet Concept | Main Command |
|---|---|---|
| 1 | File management / desired state | `puppet apply hello.pp` |
| 2 | Package management | `puppet apply package.pp` |
| 3 | Service management | `puppet apply apache.pp` |
| 4 | Facter | `facter` |
| 5 | Facter + Puppet | `puppet apply facter.pp` |
| 6 | Development in isolation | `puppet apply isolation.pp` |

## Important Commands

```bash
puppet --version
puppet apply <manifest>.pp
facter
facter os
facter hostname
facter networking.ip
systemctl status apache2
sudo systemctl start apache2
sudo systemctl stop apache2
```
