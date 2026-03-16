## Getting Started with Ansible

{% if slide %}
::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}

**Core Concept & Installation**
* **Agentless Architecture:** Operates exclusively over standard SSH.
* **Declarative State:** Infrastructure state is defined using YAML syntax.

:::
:::{grid-item-card} Installation:
```bash
python3 -m pip install ansible-core

```
:::
::::
{% endif %}

{% if page %}
Ansible is an open-source, agentless IT automation engine utilized for configuration management, application deployment, and infrastructure provisioning.

Unlike traditional configuration management tools, Ansible does not require custom agents to be installed on target nodes. Instead, it leverages standard SSH connections to execute modules and transfer configurations. Furthermore, Ansible operates on a declarative paradigm: the desired state of the system is described in YAML-based files called "playbooks," and the Ansible engine autonomously determines the necessary steps to achieve and maintain that state. This enforces **idempotency**, meaning a playbook can be executed repeatedly without causing unintended changes if the system is already in the desired state.

**Installation:**
To adhere to the principle of environmental isolation, Ansible should be installed within a dedicated Python virtual environment. The `ansible-core` package provides the fundamental execution engine and CLI tools.

```bash
python3 -m pip install ansible-core

```

{% endif %}

{% if slide %}
::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item-card}{% else %}###{% endif %} Playbooks and Execution

{% if slide %}
**Minimal Inventory (`inventory.ini`):**
Defines the target nodes.

```ini
[webservers]
192.168.1.50 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

**Minimal Playbook (`setup.yml`):**
Declares the desired state.

```yaml
---
- name: Ensure configuration directory exists
  hosts: webservers
  tasks:
    - name: Create app directory
      ansible.builtin.file:
        path: /opt/myapp/config
        state: directory
        mode: '0755'

```

**Execution Command:**

```bash
ansible-playbook -i inventory.ini setup.yml

```

{% endif %}

{% if page %}
Execution in Ansible requires two primary components: an **inventory** file defining the target infrastructure, and a **playbook** defining the tasks to be executed.

**The Inventory File:**
The inventory file maps network addresses to logical groups. For example, a file named `inventory.ini` defines a group of target nodes and their access parameters:

```ini
[webservers]
192.168.1.50 ansible_user=ubuntu

```

**The Playbook:**
Playbooks are YAML documents containing one or more "plays". Each play targets specific hosts from the inventory and defines a sequence of tasks. The following minimal playbook (`setup.yml`) ensures a specific directory structure exists on the target nodes:

```yaml
---
- name: Ensure configuration directory exists
  hosts: webservers
  become: yes  # Elevate privileges (sudo)
  tasks:
    - name: Create app directory
      ansible.builtin.file:
        path: /opt/myapp/config
        state: directory
        mode: '0755'

```

**Execution:**
The playbook is applied to the infrastructure utilizing the `ansible-playbook` command, passing the inventory file and the playbook as arguments:

```bash
ansible-playbook -i inventory.ini setup.yml

```

{% endif %}

{% if slide %}
:::
:::{grid-item-card}{% else %}###{% endif %} Managing Secrets: Ansible Vault

{% if slide %}
**Cryptographic Variable Isolation**

* Hardcoding sensitive data (API keys, database passwords) in version control violates security and SoC principles.
* **Ansible Vault** provides native AES256 encryption for files and variables.

**Vault Workflow:**

```bash
# 1. Create an encrypted variables file
ansible-vault create secrets.yml

# 2. Reference the variables in a playbook
# ... inside playbook ...
# password: "{{ db_password }}"

# 3. Execute the playbook, prompting for the decryption key
ansible-playbook -i inventory.ini setup.yml --ask-vault-pass

```
:::
::::
{% endif %}

{% if page %}
Storing plaintext credentials, API tokens, or database passwords within Git repositories represents a critical security vulnerability and a failure to isolate dynamic state (Pillar 1 of SoC). Ansible Vault provides a native mechanism to encrypt variables and files using AES256 encryption, allowing sensitive data to be safely committed to version control alongside standard configuration files.

**Creating Encrypted Secrets:**
An encrypted YAML file containing sensitive variables is generated using the `ansible-vault create` command. The user is prompted to establish a master decryption password.

```bash
ansible-vault create secrets.yml

```

Inside the encrypted file, variables are defined using standard YAML syntax (e.g., `db_password: SuperSecretString123`).

**Integration and Execution:**
The encrypted file is subsequently included in the playbook execution. When the playbook is executed, the decryption key must be provided to the engine. This is typically achieved using the `--ask-vault-pass` flag for interactive execution, or via a securely stored `--vault-password-file` for automated CI/CD pipelines.

```bash
# Execute the playbook and prompt the user for the vault password
ansible-playbook -i inventory.ini --extra-vars "@secrets.yml" setup.yml --ask-vault-pass

```

During execution, Ansible dynamically decrypts the contents of `secrets.yml` in memory, substitutes the variables (e.g., `{{ db_password }}`) into the required module parameters, and completes the provisioning process without exposing the plaintext secrets to standard output or log files.
{% endif %}
