# Unit III — Nagios Monitoring

## Overview

Nagios is an open-source monitoring system used to monitor the availability, performance, and health of IT infrastructure. It can monitor servers, network devices, applications, services, websites, and other resources.

This unit covers continuous monitoring concepts, Nagios architecture and features, plugins, states, installation, configuration, web-based monitoring, command-line interfaces, and deployment of a simple web application.

---

# 1. Continuous Monitoring Concepts

## 1.1 Definition

Continuous monitoring is the process of continuously observing IT systems, applications, servers, services, and network resources to identify failures, performance issues, and availability problems.

Instead of checking systems manually, monitoring tools automatically check the status of resources and generate alerts when problems occur.

### Example

A monitoring system can continuously check:

- Whether a web server is running
- Whether a website is accessible
- CPU utilization
- Memory utilization
- Disk space
- Network availability
- Database availability
- Application services

---

## 1.2 Importance of Continuous Monitoring

Continuous monitoring is important because modern applications and infrastructure need to remain available and reliable.

### Major reasons

1. **Early Problem Detection**
   - Problems can be detected before they become major failures.

2. **Improved Availability**
   - Monitoring helps ensure that important services remain available.

3. **Faster Troubleshooting**
   - Alerts provide information about failed or unhealthy resources.

4. **Performance Monitoring**
   - Administrators can monitor system resources and identify performance problems.

5. **Reduced Downtime**
   - Early detection allows administrators to respond quickly.

6. **Better Infrastructure Management**
   - Monitoring provides visibility into servers, applications, and services.

---

# 2. Introduction to Nagios

## 2.1 What is Nagios?

Nagios is an open-source monitoring and alerting system.

It can monitor:

- Hosts
- Servers
- Network devices
- Applications
- Web servers
- Network services
- System resources

Nagios performs checks at regular intervals and reports the status of monitored resources.

---

## 2.2 Features of Nagios

Important features include:

- Host monitoring
- Service monitoring
- Network monitoring
- Application monitoring
- Alerting
- Event handling
- Web-based monitoring interface
- Plugin-based architecture
- Notification support
- Performance monitoring
- Downtime scheduling
- Comment management
- Custom monitoring checks

---

# 3. Nagios Architecture

Nagios uses a modular architecture.

The major components include:

```text
                 +-------------------+
                 |   Nagios Server   |
                 +---------+---------+
                           |
             +-------------+-------------+
             |                           |
       Configuration                 Scheduler
             |                           |
             +-------------+-------------+
                           |
                       Plugins
                           |
          +----------------+----------------+
          |                |                |
       Servers          Services        Devices
          |                |                |
       HTTP/SSH         HTTP/DNS        Network


Main Components
3.1 Nagios Core

Nagios Core is responsible for:

Scheduling checks
Processing monitoring results
Handling events
Generating notifications
Managing hosts and services
3.2 Configuration Files

Nagios configuration files define:

Hosts
Services
Commands
Contacts
Contact groups
Time periods
Host groups
Service groups
3.3 Plugins

Plugins perform the actual checks.

Examples:

check_ping
check_http
check_ssh
check_dns
check_disk
check_load

The Nagios Core schedules a check and the plugin performs the check.

3.4 Notifications

Nagios can notify administrators when a monitored resource changes state.

Notifications may be generated for:

Host failures
Service failures
Recovery
Warning conditions
Critical conditions
3.5 Web Interface

The Nagios web interface provides a graphical view of:

Hosts
Services
Problems
Downtimes
Comments
Monitoring information

4. Nagios Plugins

Plugins are programs used by Nagios to perform monitoring checks.

A plugin receives information about a resource and returns a status.

Typical plugin states are:

OK
WARNING
CRITICAL
UNKNOWN
Example

For an HTTP check:

./check_http -H localhost

A successful check may return:

HTTP OK
5. Nagios States

Nagios uses different states to represent the status of hosts and services.

5.1 OK

The resource is operating normally.

Example:

HTTP OK
5.2 WARNING

The resource is functioning but has crossed a warning threshold.

Example:

Disk usage is above the warning threshold.
5.3 CRITICAL

The monitored resource has a serious problem.

Example:

Web server is not responding.
5.4 UNKNOWN

Nagios cannot determine the status of the resource.

This may happen because:

A plugin failed
Configuration is incorrect
Required information is unavailable
6. Soft and Hard States

Nagios uses the concepts of soft and hard states to prevent temporary problems from immediately generating notifications.

Soft State

A soft state occurs when a problem is detected but has not yet been confirmed through repeated checks.

Example:

First failed check
        ↓
Soft CRITICAL
        ↓
Additional checks

If the problem disappears, the service may return to OK without generating a major alert.

Hard State

A hard state occurs when Nagios confirms the problem after the configured number of retries.

Example:

Check 1 → CRITICAL
Check 2 → CRITICAL
Check 3 → CRITICAL
             ↓
       HARD CRITICAL

The hard state can trigger notifications.

7. Installation of Nagios

Nagios installation can be performed using package managers or by compiling the software from source.

The general installation process includes:

Installing prerequisites
Installing Nagios
Installing plugins
Configuring the web server
Creating users
Configuring monitoring
Starting services
Accessing the web interface
8. Installation Using Package Managers

Different Linux distributions use different package management systems.

Debian/Ubuntu

Common commands include:

sudo apt-get update

and:

sudo apt-get install nagios4

Depending on the Linux distribution and available repositories, package names may differ.

RHEL/CentOS

RPM-based distributions commonly use:

sudo yum install nagios

or:

sudo dnf install nagios

The exact package availability depends on the operating system and repositories.

9. Installing Prerequisites

Before compiling Nagios, required development packages and libraries must be installed.

Typical prerequisites may include:

gcc
make
apache2
php
libapache2-mod-php
unzip
wget

For RPM-based systems, equivalent packages can be installed using:

yum

or:

dnf
10. Compiling and Installing Nagios

A general source installation process is:

wget <nagios-source-package>

Extract the source:

tar -xzf <nagios-package>.tar.gz

Enter the directory:

cd <nagios-directory>

Configure the build:

./configure

Compile:

make all

Install:

sudo make install

Install configuration files:

sudo make install-config

Install the web configuration:

sudo make install-webconf

The exact commands may vary according to the Nagios version and operating system.

11. Setting Up the Web Server

Nagios commonly uses a web server such as Apache to provide its web interface.

The general process includes:

Install Apache
Install required PHP components
Configure Nagios web files
Configure authentication
Start Apache
Start Nagios

Example Apache installation:

sudo apt-get install apache2

Start Apache:

sudo systemctl start apache2

Enable Apache at boot:

sudo systemctl enable apache2
12. Nagios Web Interface

After installation and configuration, Nagios can be accessed through a browser.

A typical URL is:

http://localhost/nagios

or:

http://<server-ip>/nagios

The web interface provides information about monitored infrastructure.

13. Command-Line Interfaces

Nagios can also be managed and tested through the command line.

Useful commands include:

nagios -v /usr/local/nagios/etc/nagios.cfg

This checks the Nagios configuration.

To check the service:

sudo systemctl status nagios

To start Nagios:

sudo systemctl start nagios

To restart Nagios:

sudo systemctl restart nagios

To enable Nagios at startup:

sudo systemctl enable nagios
14. Nagios Configuration

Nagios configuration defines what should be monitored and how monitoring should be performed.

Important configuration objects include:

Hosts
Services
Commands
Contacts
Contact groups
Time periods
Host groups
Service groups
15. Monitoring Hosts

A host represents a physical or virtual machine or network device.

Example:

Host:
    Name: web-server
    Address: 192.168.1.10

A host configuration can contain information such as:

host_name
alias
address
check_command
max_check_attempts
notification_interval
16. Monitoring Services

A service represents something running on a host.

Examples:

HTTP
SSH
DNS
FTP
Database
Disk
CPU
Memory

Example:

Host: web-server
Service: HTTP
Check: check_http
17. Monitoring a Web Server

Nagios can monitor an HTTP service using the check_http plugin.

Example:

/usr/local/nagios/libexec/check_http -H localhost

A successful response may look similar to:

HTTP OK

A failed check may produce a critical result.

18. Example HTTP Service Configuration

A simplified service definition may look like:

define service {
    use                     generic-service
    host_name               web-server
    service_description     HTTP
    check_command           check_http
}

This tells Nagios to monitor the HTTP service of the specified host.

19. Using the Built-in Web Interface

The Nagios web interface can be used to manage and inspect monitoring information.

Important areas include:

Hosts
Services
Problems
Downtimes
Comments
Monitoring information
20. Managing Hosts

The Hosts section provides information about monitored machines.

It can show:

Host name
Host status
IP address
Last check
Next check
Status information
Availability information

Example:

Host: web-server
Status: UP
Address: 192.168.1.10
21. Managing Services

The Services section displays the status of individual services.

Example:

Service        Status
-------------------------
HTTP           OK
SSH            OK
DNS            OK

If a service fails:

HTTP           CRITICAL

Nagios can then generate a notification according to its configuration.

22. Downtimes

Downtime allows administrators to tell Nagios that a planned maintenance period is expected.

Example:

Web Server Maintenance
Start: 10:00 PM
End:   11:00 PM

During planned downtime, notifications can be handled according to the configured downtime rules.

23. Comments

Comments can be associated with hosts and services.

They can be used to record information such as:

Maintenance details
Troubleshooting information
Reason for a known failure
Administrative notes

Example:

Comment:
Web server is undergoing planned maintenance.
24. Information in the Web Interface

Nagios provides information such as:

Current status
Last check
Next scheduled check
Status duration
Attempt number
Performance information
Plugin output
Availability history

This information helps administrators understand the current condition of monitored resources.

25. Deploying a Simple Web Application on a Server

A simple web application can be deployed on a Linux server and monitored using Nagios.

For example, Apache can be installed:

sudo apt update
sudo apt install apache2

Start Apache:

sudo systemctl start apache2

Enable it at startup:

sudo systemctl enable apache2

Check Apache:

sudo systemctl status apache2
26. Creating a Simple Web Page

The default web directory may be:

/var/www/html/

Create a simple page:

sudo nano /var/www/html/index.html

Example content:

<!DOCTYPE html>
<html>
<head>
    <title>Nagios Monitoring Demo</title>
</head>
<body>
    <h1>Hello from the Web Server</h1>
    <p>This web application is being monitored using Nagios.</p>
</body>
</html>

Save the file.

27. Testing the Web Application

Open the server address in a browser:

http://localhost

or:

http://<server-ip>

The page should display:

Hello from the Web Server
28. Monitoring the Web Application with Nagios

Once the web server is running, Nagios can monitor it using the HTTP plugin.

Example:

check_http -H localhost

Expected result:

HTTP OK

Nagios can then continuously check the web server.

29. Example Monitoring Flow

The complete monitoring flow can be represented as:

                Nagios Core
                    |
                    |
              Schedule Check
                    |
                    v
              check_http
                    |
                    v
              Web Server
                    |
          +---------+---------+
          |                   |
        HTTP OK          HTTP Failure
          |                   |
          v                   v
        OK State          CRITICAL State
          |                   |
          +---------+---------+
                    |
                    v
              Web Interface
                    |
                    v
              Administrator
30. Complete Unit III Revision Summary
Continuous Monitoring

Continuous monitoring continuously checks infrastructure and services to identify failures and performance issues.

Nagios

Nagios is an open-source monitoring and alerting system.

Major Nagios Components
Nagios Core
Configuration files
Plugins
Notifications
Web interface
Important States
OK
WARNING
CRITICAL
UNKNOWN
Soft State

A problem that has not yet been confirmed through the required number of checks.

Hard State

A confirmed state after repeated checks.

Important Plugins
check_http
check_ping
check_ssh
check_dns
check_disk
check_load
Installation Methods

Nagios can be installed using:

apt-get / dpkg
yum / rpm
Source compilation
Important Configuration Objects
Hosts
Services
Commands
Contacts
Contact Groups
Time Periods
Host Groups
Service Groups
Web Interface

The web interface provides information about:

Hosts
Services
Problems
Downtimes
Comments
Monitoring Information
Web Server Monitoring

Nagios can use:

check_http

to verify whether an HTTP server is responding.

Simple Web Application

A basic web application can be deployed using Apache and monitored by Nagios.

Unit III — Important Commands
Update package information
sudo apt update
Install Apache
sudo apt install apache2
Start Apache
sudo systemctl start apache2
Check Apache
sudo systemctl status apache2
Enable Apache
sudo systemctl enable apache2
Validate Nagios configuration
nagios -v /usr/local/nagios/etc/nagios.cfg
Check Nagios service
sudo systemctl status nagios
Start Nagios
sudo systemctl start nagios
Restart Nagios
sudo systemctl restart nagios
Test HTTP monitoring
/usr/local/nagios/libexec/check_http -H localhost
Quick Revision
Continuous Monitoring
        ↓
Nagios
        ↓
Nagios Core
        ↓
Plugins
        ↓
Hosts + Services
        ↓
Monitoring Checks
        ↓
OK / WARNING / CRITICAL / UNKNOWN
        ↓
Web Interface
        ↓
Alerts and Administration


Key Exam Points
Nagios is an open-source monitoring and alerting system.
Nagios Core schedules and processes monitoring checks.
Plugins perform actual monitoring operations.
Hosts represent systems or devices.
Services represent applications or services running on hosts.
Nagios uses OK, WARNING, CRITICAL, and UNKNOWN states.
Soft states represent unconfirmed problems.
Hard states represent confirmed states.
check_http can be used to monitor web servers.
The Nagios web interface provides graphical monitoring information.
Downtime is used for planned maintenance periods.
Comments can be used to record administrative information.
Nagios configuration defines hosts, services, commands, contacts, and other monitoring objects.
Nagios can monitor web servers and simple web applications.
Continuous monitoring helps detect infrastructure problems quickly.