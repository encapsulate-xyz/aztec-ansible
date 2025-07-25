# Ansible Playbook to deploy Aztec Sequencer Node

This Ansible playbook automates the deployment and configuration of Aztec Sequencer Node. It ensures that the necessary dependencies, configuration files, and services are properly set up and running.

## Table of Contents

- [Requirements](#requirements)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Variables](#variables)
- [Usage](#usage)

## Requirements

Before using this playbook, ensure the following requirements are met:

1. **Ansible version**: Make sure you have Ansible 2.15+ installed.
2. **SSH Access**: Passwordless SSH access to all target servers.
3. **Python**: Python 3.x installed on the control node and all target hosts.
4. **Privileges**: The user running the playbook must have sudo privileges on the target machines.

## Prerequisites

**Install HashiCorp Vault**

This playbook relies on HashiCorp Vault to securely retrieve sensitive files, such as validator keys and secrets. Follow the [HashiCorp Vault Installation Guide](https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install) to set up Vault on your infrastructure.

**Note on Secrets Management**

The playbook dynamically retrieves private keys and environment secrets from HashiCorp Vault. The keys and secrets follow a structured path format:
`<environment>/<project>/<organization>/<type>/<file_name>`
For example:
`testnet/aztec/encapsulate/sequencer/p2p-private-key`

Example secret environment file:
[`testnet/aztec/encapsulate/validator/aztec.secrets.env`](roles/sequencer/templates/testnet/secrets.env.example)

This structure ensures easy organization and secure retrieval of secrets.

## Setup

### 1. Install Ansible

If Ansible is not installed, visit the official documentation for detailed instructions on how to install Ansible on various Linux distributions:

[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)

### 2. Clone the repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/encapsulate-xyz/aztec-ansible.git
cd aztec-ansible
```

### 3. Inventory

Define your target servers' IP address or DNS in the inventory folder, and select either `mainnet.yml` or `testnet.yml` to update.

Example for `testnet.yml`

```yaml
---
# maintains testnet inventory grouped by project names
all:
  vars:
    env: testnet
  children:
    aztec:
      hosts:
        sequencer.aztec.testnet.encapsulate.xyz:
          type: sequencer
```

## Variables

This playbook allows customization through several variables. You can define these variables in the following locations:

- **`group_vars/all.yml`**: Contains all the port, source URL configurations.
- **`group_vars/mainnet.yml`** or **`group_vars/testnet.yml`**: Contains version-specific variables.

**Note**: Sensitive variables such as private keys and passwords are not stored in `vault.yml`. They are dynamically retrieved from HashiCorp Vault during the playbook run.

### Usage

1. First, install the dependencies:

```bash
ansible-galaxy role install -r roles/requirements.yml
ansible-galaxy collection install -r collections/requirements.yml
```

2. Configure your remote server username and private key file path in the `ansible.cfg` file. Additionally, set the SSH port for your server by adjusting the `ansible_port` variable in `group_vars/all.yml`.

3. Run the playbook:

- To deploy a aztec sequencer node:

```bash
ansible-playbook setup_node.yml -i inventory/testnet.yml --limit sequencer.aztec.testnet.encapsulate.xyz
```

**Example Output:**

```bash
TASK [Display environment being deployed to] ***************************************************************************************************
ok: [sequencer.aztec.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: sequencer.aztec.testnet.encapsulate.xyz",
        "Groups: ['aztec']",
        "Project: aztec",
        "Environment: testnet",
        "Type: sequencer",
        "Version: v1.0.0",
        "Service Name: aztec",
        "Operating System: linux",
        "System Architecture: amd64"
    ]
}

TASK [Confirm deployment details] **************************************************************************************************************
[Confirm deployment details]
Press 'Enter' to continue with the deployment or Ctrl+C to cancel:
ok: [sequencer.aztec.testnet.encapsulate.xyz]

TASK [Please confirm again] ********************************************************************************************************************
ok: [sequencer.aztec.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: sequencer.aztec.testnet.encapsulate.xyz",
        "Project: aztec",
        "Environment: testnet",
        "Type: sequencer"
    ]
}

TASK [Confirm deployment details] **************************************************************************************************************
[Confirm deployment details]
Press 'Enter' to continue with the deployment or Ctrl+C to cancel:
ok: [sequencer.aztec.testnet.encapsulate.xyz]
```
