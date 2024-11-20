# Satellite - Configuration as Code

This repository contains an Ansible playbook designed to automate the installation and configuration of Red Hat Satellite using Infrastructure as Code (IaC) principles. The playbook is intended to be run within Ansible Automation Platform (AAP) or AWX and supports both production and test environments.

## Overview

The Ansible playbook in this repository automates the installation and configuration of Red Hat Satellite. It performs the following high-level tasks:

1. **Sets up system prerequisites** required for Satellite installation.
2. **Installs Red Hat Satellite** on the target hosts.
3. **Configures Satellite** with necessary settings, organizations, locations, repositories, and more.

## Prerequisites

- **Ansible Automation Platform (AAP) or AWX**: The playbook is designed to run within AAP or AWX.
- **Ansible Version**: Ansible 2.9 or higher is recommended.
- **Access to Target Hosts**: Ensure you have SSH access to the hosts where Satellite will be installed.
- **Red Hat Subscription**: Required for registering the hosts and accessing Satellite repositories.
- **Ansible Collections**:
    - `community.general`
    - `redhat.satellite`
    - `redhat.satellite_operations`

## Configuration

### Variables

Variables are defined in the `vars/` directory for different environments:

- `prod.yml`: Variables for the production environment.
- `test.yml`: Variables for the test environment.

Look through roles/satellite_configure/tasks for specific settings for your Satellite environment.

### Run Specific Tags

You can run specific roles or tasks using tags:

- **Prerequisites Only**: prereq
- **Installation Only**: install
- **Configuration Only**: configure

## Playbook Structure

- **`site.yml`**: The main playbook file that orchestrates the roles.
- **`vars/`**: Contains environment-specific variable files (`prod.yml`, `test.yml`).
- **`collections/requirements.yml`**: Specifies the required Ansible collections.
- **`roles/`**: Contains the roles for different stages of the setup:
    - `satellite_prereq`: Prepares the system for Satellite installation.
    - `satellite_install`: Installs Red Hat Satellite.
    - `satellite_configure`: Configures Satellite settings and resources.

## Roles and Tasks Breakdown

### Prerequisite Role (`satellite_prereq`)

This role prepares the system for Satellite installation by performing the following tasks:

- **Firewall Configuration**:
    
    - Enables necessary services and ports using `firewalld`.
    - Services: DNS, DHCP, TFTP, HTTP, HTTPS, MQTT.
    - Ports: Ranges and specific ports required by Satellite and its components.
- **Subscription Management**:
    
    - Registers the host to Red Hat Subscription Management using `community.general.redhat_subscription`.
    - Configures necessary repositories for Satellite installation.
- **Package Management**:
    
    - Enables the Satellite module stream for `dnf`.
    - Installs the Satellite package and other required packages (`chrony`, `sos`).
- **Service Management**:
    
    - Ensures `chronyd` is enabled and started for time synchronization.
- **SELinux Configuration**:
    
    - Adds SELinux exceptions for services like Cockpit and LDAP authentication.

### Installation Role (`satellite_install`)

This role handles the installation of Red Hat Satellite:

- **System Update**:
    
    - Performs a full system update to ensure all packages are up to date.
- **Satellite Installation Variables**:
    
    - Sets necessary variables for the Satellite installer, such as initial organization, location, admin username, and password.
- **Satellite Installation**:
    
    - Invokes the Satellite installer using the `redhat.satellite_operations.installer` role.
- **Manifest Import**:
    
    - Imports the appropriate subscription manifest based on the environment (`prod` or `test`).

### Configuration Role (`satellite_configure`)

This role configures Satellite with various settings and resources:

- **Settings**:
    
    - Configures general settings like Satellite URL, default language, timezone, and provisioning URLs.
- **Locations and Organizations**:
    
    - Sets up locations (`On Campus`, `AWS`) and associates them with the organization `ISU`.
- **Synchronization Plans**:
    
    - Creates sync plans for content synchronization (e.g., `Daily 2AM`).
- **Repositories and Products**:
    
    - Configures Red Hat and custom repositories (e.g., Microsoft, EPEL).
    - Adds GPG keys and content credentials.
- **Lifecycle Environments**:
    
    - Creates lifecycle environments (`Dev`, `Test`, `Prod`) to manage content promotion.
- **Content Views**:
    
    - Creates content views for different RHEL versions (7, 8, 9) with associated repositories.
- **Activation Keys**:
    
    - Creates activation keys for hosts to register and subscribe to content.
- **Host Collections**:
    
    - Sets up host collections for grouping hosts based on criteria.
- **Authentication**:
    
    - Configures LDAP authentication for the Satellite web console.
    - Sets up user groups and external user groups for role-based access control.

## License

MIT License
