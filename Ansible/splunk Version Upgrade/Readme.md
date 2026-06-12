# Splunk Universal Forwarder Upgrade Automation using Ansible

## Overview

This project automates the upgrade process of the Splunk Universal Forwarder across multiple Linux servers using Ansible.

The playbook performs the following actions:

1. Checks whether the Splunk Forwarder process is running.
2. Stops the Splunk service if it is active.
3. Identifies the currently installed Splunk package version.
4. Removes the existing package.
5. Downloads the latest Splunk Universal Forwarder package.
6. Prepares the system for installation of the upgraded version.

This automation helps reduce manual effort, ensures consistency across servers, and provides a repeatable upgrade process.

---

## Use Case

System administrators often need to upgrade Splunk Universal Forwarders across a large number of servers.

Performing these upgrades manually can result in:

- Service downtime due to human error
- Inconsistent versions across environments
- Increased operational effort
- Lack of repeatability

This playbook automates the process and standardizes the upgrade workflow.

---

## Technologies Used

- Ansible
- Linux (RHEL/CentOS)
- RPM Package Management
- Splunk Universal Forwarder

---

## Project Structure

```text
.
├── inventory
├── splunk_upgrade.yml
└── README.md
```

---

## Workflow

```text
Start
  |
  v
Check Splunk Process
  |
  v
Is Splunk Running?
  |---- No ----> Continue
  |
 Yes
  |
  v
Stop Splunk Service
  |
  v
Check Installed Version
  |
  v
Remove Existing Package
  |
  v
Download Latest Package
  |
  v
Ready for Installation
  |
  v
End
```

---

## Running the Playbook

```bash
ansible-playbook -i inventory splunk_upgrade.yml
```

---

## Prerequisites

- Ansible installed on the control node
- SSH access to target servers
- Sudo privileges on managed hosts
- Splunk Universal Forwarder installed
- Internet access to download Splunk packages

---

## Key Ansible Concepts Demonstrated

### Process Validation

```yaml
register: process
```

Stores command output for conditional execution.

### Conditional Task Execution

```yaml
when: process.stdout != ""
```

Executes tasks only when Splunk is running.

### Error Handling

```yaml
ignore_errors: true
failed_when: false
```

Allows the playbook to continue gracefully when expected conditions are not met.

### Package Management

```yaml
rpm -e
```

Removes existing Splunk packages before upgrade.

---

## Future Enhancements

- Replace shell commands with Ansible built-in modules.
- Add version comparison logic.
- Validate package checksum before installation.
- Create rollback functionality.
- Convert the playbook into reusable Ansible roles.
- Add service health checks after upgrade.
- Support both Splunk Enterprise and Universal Forwarder installations.
- Integrate with CI/CD pipelines for automated deployment.

---

## Learning Objectives

This project was created to practice:

- Ansible automation
- Linux package management
- Service lifecycle management
- Conditional task execution
- Infrastructure automation best practices

---

## Disclaimer

This repository is intended for learning and demonstration purposes. Additional validation, error handling, backup procedures, and rollback mechanisms should be implemented before using the playbook in production environments.
