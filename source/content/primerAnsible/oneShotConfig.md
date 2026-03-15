## One Shot Config

{% if page %}

The following Ansible playbook is designed to execute a standardized, reproducible system configuration.
The execution model relies on idempotency, ensuring that repeated executions yield the same system state without unintended side effects.
Secrets are managed securely via Ansible Vault to prevent plaintext exposure in version control repositories.

### Prerequisites

* Ansible installed on the control node.
* Target host accessible via SSH with privilege escalation (`sudo`) configured.
* Debian/Ubuntu-based operating system on the target (due to `apt` module usage).

{% endif %}

### Playbook Implementation (`setup.yml`)

For the full playbook rever to the [ansibleOneShot](https://github.com/pSciComp/ansibleOneShot/blob/main/setup.yml) repository.
{% if slide %}
::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}
{% endif %}

{% raw %}
```yaml
---
- name: One Shot System Configuration
  hosts: all
  become: true
  vars_files:
    - secrets.yml
  vars:
    # Default variables; can be overridden at runtime
    new_username: "sysadmin"
    ...
  tasks:
    - name: Update apt cache and upgrade packages
      ansible.builtin.apt:
        update_cache: true
        upgrade: dist
        cache_valid_time: 3600

    ...
    - name: Install system dependencies and utilities
      ansible.builtin.apt:
        name:
          - git
          ...
        state: present

    - name: Provision new user account
      ansible.builtin.user:
        name: "{{ new_username }}"
        shell: /bin/bash
        create_home: true
    ...
    - name: Define global Git user name
      community.general.git_config:
        name: user.name
        scope: global
        value: "{{ git_user_name }}"
      become_user: "{{ new_username }}"
    ...
    - name: Deploy OpenStack credentials file
      ansible.builtin.copy:
        src: "{{ openstack_rc_file }}"
        dest: "/home/{{ new_username }}/.openrc"
        owner: "{{ new_username }}"
        group: "{{ new_username }}"
        mode: '0600'
      when: openstack_rc_file is defined
```
{% endraw %}

{% if slide %}
:::
:::{grid-item}

{% endif %}

---

### Execution Protocol

{% if page %}
Reproducibility is maintained by separating code from configuration data. Secrets are isolated in an encrypted vault, while environmental paths are passed as runtime parameters.
{% endif %}

**1. Vault Initialization**
{% if page %}
An encrypted file must be created to store the internal SSH keypair.
{% endif %}

```bash
ansible-vault create secrets.yml

```

```yaml
vault_private_key: |
  -----BEGIN OPENSSH PRIVATE KEY-----
  b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
  ...
  -----END OPENSSH PRIVATE KEY-----

vault_public_key: "ssh-ed25519 AAAAC3NzaC1l... user@internal"

```

**2. Playbook Execution**
{% if page %}
The playbook is invoked using the `ansible-playbook` binary.
External variables are injected via the `-e` (extra vars) parameter, pointing to local files on the control node.
The `--ask-vault-pass` flag triggers the decryption sequence for `secrets.yml`.
{% endif %}

```bash
ansible-playbook setup.yml \
  -i inventory_file \
  --ask-vault-pass \
  -e "new_username=developer" \
  -e "public_key_file=~/.ssh/id_ed25519.pub" \
  -e "openstack_rc_file=~/project-openrc.sh"

```


**Parameter Breakdown:**

* `-i inventory_file`: Specifies the target hosts.
* `-e "public_key_file=..."`: Defines the path to the public key utilized for authenticating.
* `-e "openstack_rc_file=..."`: (Optional) Defines the path to the local OpenStack `.rc` configuration.

{% if slide %}
:::
::::

{% endif %}

