## CLI Access via `openstack`

{% if slide %}

OpenStack offers a powerful command-line interface (CLI), called [`openstack`](https://www.google.com/search?q=%5Bhttps://docs.openstack.org/python-openstackclient/latest/cli/man/openstack.html%23synopsis%5D(https://docs.openstack.org/python-openstackclient/latest/cli/man/openstack.html%23synopsis)), which is based on the [Python OpenStack SDK](https://opendev.org/openstack/openstacksdk).

The `openstack` client provides an extensive collection of commands, enabling programmatic and reproducible interaction with an OpenStack cloud.

{% else %}

For automated deployments and strict reproducibility, GUI-based instantiation is generally replaced by command-line or infrastructure-as-code methodologies. OpenStack provides a comprehensive command-line interface (CLI) named [`openstack`](https://www.google.com/search?q=%5Bhttps://docs.openstack.org/python-openstackclient/latest/cli/man/openstack.html%23synopsis%5D(https://docs.openstack.org/python-openstackclient/latest/cli/man/openstack.html%23synopsis)), which acts as a wrapper around the underlying REST APIs via the [Python OpenStack SDK](https://opendev.org/openstack/openstacksdk).

The CLI enables the programmatic orchestration of cloud resources, mitigating the risk of human error inherent in manual graphical configurations.

{% endif %}

{% if slide %}
::::{grid}
:gutter: 2

:::{grid-item-card}{% else %}###{% endif %} Retrieving Authentication Credentials

{% if slide %}
* **Requirement:** The CLI requires specific environment variables to authenticate API requests.
* **GUI Navigation:** Top-right User Dropdown → **<i class="fa-solid fa-download"></i> OpenStack RC File**.
* **Result:** Downloads a shell script (e.g., `project-openrc.sh`) containing the required credentials.

{% else %}


Before the CLI can be utilized, authentication credentials must be secured.
The OpenStack dashboard (Horizon) automatically generates a shell script containing the necessary environment variables (such as `OS_AUTH_URL`, `OS_PROJECT_ID`, and `OS_USERNAME`) required by the client.

1. Navigate to the OpenStack Web GUI.
2. Access the user profile dropdown menu located in the top-right corner of the interface.
3. Select **OpenStack RC File v3** to initiate the download of the configuration file (typically named `<project-name>-openrc.sh`).

This file must be present in the local environment where the `openstack` CLI is executed.

{% endif %}

{% if slide %}
:::
:::{grid-item-card}{% else %}###{% endif %} Execution Example

{% if slide %}

```bash
# 1. Authenticate the session using the downloaded RC file
source project-openrc.sh

# openstack keypair create --public-key ~/.ssh/id_ed25519.pub laptopKey

# 2. Provision the compute instance
openstack server create \
    --flavor 1cpu-4ram-hpcv3 \
    --image ubuntu-jammy-22.04 \
    --network uzh-only \
    --security-group default \
    --key-name laptopKey\
    test_instance
# 3. Show the IP of the instance
openstack server show test_instance -f value -c addresses
# 4. Cleaning up
openstack server delete test_instance
```
:::
::::

{% else %}

To replicate the previous GUI-based Virtual Machine creation, the following sequence of commands is utilized. This assumes the OpenStack client is installed and the downloaded RC file is available.

```bash
# 1. Authenticate the terminal session
# This file exports the required OS_* variables into the shell.
# A prompt will appear requesting the OpenStack user password.
source project-openrc.sh

# 2. Establish the SSH Key Pair
# The local public key is registered with the OpenStack compute service
openstack keypair create --public-key ~/.ssh/mysecret.pub laptopKey

# 3. Provision the Compute Instance
# The server is instantiated by explicitly defining all required dependencies
openstack server create \
    --flavor 1cpu-4ram-hpcv3 \
    --image ubuntu-jammy-22.04 \
    --network uzh-only \
    --security-group default \
    --key-name laptopKey \
    test_instance


# 4. Show the IP of the instance
openstack server show test_instance -f value -c addresses

# 5. Cleaning up
openstack server delete test_instance
```

By explicitly declaring the infrastructure requirements as code, the environment setup becomes verifiable, version-controllable, and strictly reproducible across different sessions or cluster deployments.

{% endif %}
