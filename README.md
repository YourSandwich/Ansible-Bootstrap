# Ansible-Bootstrap

An Ansible playbook for automating system bootstrap processes in an Infrastructure-as-Code manner, utilizing ArchISO as the foundational tool.

# Info
Most of the roles are adaptable for use with systems beyond ArchLinux, requiring only that the target system can install a necessary package manager, such as `dnf` for RHEL-based systems. Additionally, a replacement for the `arch-chroot` command may be required for these systems.

**NOTE**:
- For RHEL 8 and RHEL 9, repository access requires the `rhel_iso` variable. This variable specifies a local ISO or proxy repository.
- RHEL systems do not support `btrfs`. Use `ext4` or `xfs` as alternatives.
- For RHEL 8, `xfs` may cause installation issues; `ext4` is recommended.

# Supported Distributions

This playbook supports multiple Linux distributions with specific versions tailored to each. Below is a list of supported distributions:

| `os`       | Distribution                       |
|------------|------------------------------------|
| archlinux  | ArchLinux (Latest rolling release) |
| almalinux  | AlmaLinux 9.x                      |
| debian11   | Debian 11 (Bullseye)               |
| debian12   | Debian 12 (Bookworm)               |
| fedora     | Fedora 41                          |
| rhel8      | Red Hat Enterprise Linux 8         |
| rhel9      | Red Hat Enterprise Linux 9         |
| rocky      | Rocky Linux 9.x                    |
| ubuntu     | Ubuntu 24.10 (Oracular Oriole)     |
| ubuntu-lts | Ubuntu 24.04 LTS (Noble Numbat)    |

# Documentation

## Table of Contents

1. [Overview](#1-overview)
2. [Global Variables](#2-global-variables)
3. [Inventory Variables](#3-inventory-variables)
4. [How to Use the Playbook](#4-how-to-use-the-playbook)
   - 4.1 [Prerequisites](#41-prerequisites)
   - 4.2 [Running the Playbook](#42-running-the-playbook)
   - 4.3 [Example Usage](#43-example-usage)

## 1. Overview

The playbook uses the ArchLinux ISO as a foundational tool to provides an efficient and systematic method for the automatic deployment of a variety of Linux distributions on designated target systems. It ensures a standardized setup across different platforms, equipping each system with the essential configurations and software necessary for its designated role.

## 2. Global Variables

Global variables apply across your Ansible project and are loaded from `vars.yml` by default. These variables define common settings such as hypervisor connection details and the boot ISO path. They can be overridden by inventory variables for specific hosts or VMs if needed.

| Variable              | Description                                                        | Example Value                           |
|-----------------------|--------------------------------------------------------------------|-----------------------------------------|
| `boot_iso`            | Path to the boot ISO image.                                        | `local-btrfs:iso/archlinux-x86_64.iso`  |
| `rhel_iso`            | Path to the RHEL ISO file, required for RHEL 8 and RHEL 9.         |`local-btrfs:iso/rhel-9.4-x86_64-dvd.iso`|
| `hypervisor`          | Type of hypervisor.                                                | `libvirt`, `proxmox`, `vmware`, `none`  |
| `hypervisor_cluster`  | Name of the hypervisor cluster.                                    | `default-cluster`                       |
| `hypervisor_node`     | Hypervisor node name.                                              | `node01`                                |
| `hypervisor_password` | Password for hypervisor authentication.                            | `123456`                                |
| `hypervisor_storage`  | Storage identifier for VM disks.                                   | `local-btrfs`                           |
| `hypervisor_url`      | URL/IP address for the hypervisor interface.                       | `192.168.0.2`                           |
| `hypervisor_username` | Username for hypervisor authentication.                            | `root@pam`                              |
| `install_drive`       | Drive where the system will be installed.                          | `/dev/sda`                              |
| `install_type`        | Type of installation.                                              | `virtual`, `physical`                   |
| `vlan_name` (optional)| VLAN for the VM's network interface.                               | `vlan100`                               |

## 3. Inventory Variables

Inventory variables are defined for individual hosts or VMs in the inventory file, allowing customization of settings such as the operating system, filesystem, and compliance with CIS benchmarks. These variables can be set globally and overridden for specific hosts or VMs.

| Variable                | Description                                                                       | Example Value                                      |
|-------------------------|-----------------------------------------------------------------------------------|----------------------------------------------------|
| `cis` (optional)        | Adjusts the installation to be CIS level 3 conformant.                            | `true`, `false`                                    |
| `filesystem`            | Filesystem type for the VM's primary storage.                                     | `btrfs`, `ext4`, `xfs`                             |
| `hostname`              | The hostname assigned to the virtual machine or system.                           | `vm01`                                             |
| `os`                    | Operating system to be installed on the VM.                                       | `archlinux`, `almalinux`, `debian11`, `debian12`, `fedora`, `rocky`, `ubuntu`, `ubuntu-lts` |
| `root_password`         | Root password for the VM or system, used for initial setup or secure access.      | `SecurePass123`                                    |
| `user_name`             | Username for a user account within the VM, often used with cloud-init.            | `adminuser`                                        |
| `user_password`         | Password for the user account within the VM.                                      | `UserPass123`                                      |
| `vm_ballo` (optional)   | Ballooning memory size for the VM, used to adjust memory allocation dynamically.  | `2048`                                             |
| `vm_cpus`               | Number of CPU cores assigned to the virtual machine.                              | `4`                                                |
| `vm_dns`                | DNS server IP address(es) for the virtual machine's network configuration.        | `1.0.0.1`, `1.1.1.1`                               |
| `vm_gw`                 | Default gateway IP address for the virtual machine's network configuration.       | `192.168.0.1`                                      |
| `vm_id`                 | Unique identifier for the virtual machine.                                        | `101`                                              |
| `vm_ip`                 | IP address assigned to the virtual machine.                                       | `192.168.0.10`                                     |
| `vm_memory`             | Amount of memory (in MB) allocated to the virtual machine.                        | `2048`                                             |
| `vm_nif`                | Network interface type or identifier for the VM's network connection.             | `vmbr0`                                            |
| `vm_path (optional)`    | Path or folder where the VM configuration or related files will be stored.        | `/var/lib/libvirt/images/`                         |
| `vm_size`               | Disk size allocated for the VM's primary storage (in GB).                         | `20`                                               |

## 4. How to Use the Playbook

### 4.1 Prerequisites

Before running the playbook, ensure you have Ansible installed and configured correctly, and your inventory file is set up with the target systems defined.

### 4.2 Running the Playbook

Execute the playbook using the `ansible-playbook` command, ensuring that all necessary variables are defined, typically by specifying a `vars.yml` file containing the required configurations.

### 4.3 Example Usage

An effective way to use the playbook involves defining all necessary configurations within a `vars.yml` file. This file should include all relevant global variables tailored to your specific deployment requirements. Additionally, you should prepare an inventory file (`inventory.yml`) that lists all the hosts along with any specific inventory variables they might need. Then, you can run the playbook as follows:

```bash
ansible-playbook -i inventory.yml -e @vars.yml main.yml
```

This command prompts Ansible to execute the `main.yml` playbook, applying configurations defined in both `vars.yml` and the inventory file.
