{% if slide %}
## Creating a VM via the Web GUI

```{admonition} <i class="fa-solid fa-display"></i> Hands-on
:class: warning
Everyone clicks through the creation of a VM together.
```

### Steps

::::{grid} 1 1 2 2
:gutter: 2

:::{grid-item-card} 1. Launch Instance
- Navigate to **Compute → Instances → Launch Instance**
- Choose a **name** (e.g. `test<username>`)
- Pick a **boot source** (image: `ubuntu-jammy-22.04`)
  ```{admonition} "Create New Volume"
  :class: tip
  This allows you to create a persising block storage as boot volume.
  ```
- Select a **flavor** (e.g. `1cpu-4ram-hpcv3`)
:::

:::{grid-item-card} 2. Network & Security
- Assign to an existing **network** (e.g. `uzh-only`)
- Configure **Network Ports** (e.g., leave them empty for now)
- Configure **Security Group** (e.g., `default` which opens port 22 for SSH)
- Add your **SSH key pair**: 
   - Upload your `~/.ssh/mysecret.pub`
   - Key Pair Name: e.g. `mysecret`
   - Key Type: `SSH Key`
:::

:::{grid-item-card} 3. Connect
**SSH access:**
```bash
ssh ubuntu@<floating-ip>
```

**Web console:**
Use the built-in **Console** tab in the GUI for direct terminal access.
:::

:::{grid-item-card} 4. Cleanup
- **Stop** the VM when not in use
- **Delete** the instance when done
- Verify that **block storage volumes** persist independently

```{admonition} "Shelved Instances"
:class: warning
It does not matter if an instance runs or not **only shelved instances are not billed**.
```
:::
::::

:::{admonition} Block Storage Persistence
:class: tip
When you delete a VM, attached block storage volumes remain.
They can be re-attached to a new instance later.
:::

{% else %}

## Creating a VM via the Web GUI

The instantiation of a Virtual Machine (VM) via the Web GUI requires a sequential configuration process. This process defines the compute resources, operating system, network topology, and access security of the instance.

```{admonition} <i class="fa-solid fa-display"></i> Hands-on Walkthrough
:class: warning
This section is executed as a guided, hands-on exercise. Specific naming conventions and networks (e.g., `uzh-only`) correspond to the provided training environment.

```

### 1. Instance Configuration and Launch

The initial phase dictates the core identity and hardware specifications of the virtual machine.

* **Navigation:** The instance creation wizard is accessed via **Compute → Instances → Launch Instance**.
* **Instance Details:** A unique identifier must be allocated (e.g., `test<username>`).
* **Boot Source:** This selection determines the operating system. Choosing a specific image (e.g., `ubuntu-jammy-22.04`) provisions the VM with that OS environment.
```{admonition} Booting from Volume
:class: tip
Selecting the **"Create New Volume"** option alters default ephemeral storage behavior. A persistent block storage volume is created and attached as the boot disk, ensuring the root filesystem and OS state persist independently of the compute instance lifecycle.

```


* **Flavor:** The flavor dictates the allocated hardware resources. A selection such as `1cpu-4ram-hpcv3` assigns 1 virtual CPU core and 4 GB of memory.

### 2. Network and Security Provisioning

Following hardware definition, network connectivity and access controls are established.

* **Network Assignment:** The instance must be attached to a virtual network (e.g., `uzh-only`) to enable communication. Network Ports can typically remain unassigned for basic configurations, allowing automated IP allocation.
* **Security Groups:** These function as virtual firewalls applied at the instance level. Applying the `default` security group typically configures ingress rules to permit SSH traffic (TCP port 22) while blocking unauthorized access.
* **Key Pair Injection:** Password authentication is disabled by default on cloud images. Secure access requires public-key cryptography.
* The contents of a local public key (e.g., `~/.ssh/mysecret.pub`) are uploaded to the cloud environment.
* This key is injected into the VM's `~/.ssh/authorized_keys` file during the boot process, enabling subsequent authentication.



### 3. Establishing a Connection

Upon successful provisioning, the instance is accessed for administration and workload execution.

* **SSH Access:** The primary interaction method is via Secure Shell (SSH), utilizing the assigned Floating IP and the default OS user (e.g., `ubuntu`).
```bash
ssh ubuntu@<floating-ip>

```


* **Web Console:** If SSH access fails due to local network restrictions, the built-in **Console** tab within the Web GUI provides direct, out-of-band terminal access to the instance.

### 4. Lifecycle Management and Cleanup

Strict resource management is required in cloud environments to prevent resource exhaustion and mitigate unnecessary billing.

* **Stopping vs. Deleting:** Stopping an instance halts execution, but compute resources (vCPU, RAM) and storage remain reserved. Deleting the instance permanently destroys the compute allocation.
* **Storage Persistence:** If a persistent block storage volume was utilized, it remains intact after the VM is deleted and can be re-attached to subsequent instances.


{% endif %}
