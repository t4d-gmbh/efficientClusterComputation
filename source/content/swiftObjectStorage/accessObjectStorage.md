## Accessing Object Storage

{% if slide %}

### Endpoint Retrieval & Authentication

Interaction with the object storage REST API requires the gateway URL and a valid authentication token.

:::{admonition} Internal networking
:class: tip margin
On internal netwoks it might be that requests are always routed outwards.
You can bypass this with:
```bash
export no_proxy=$no_proxy,cloud.science-it.uzh.ch
export NO_PROXY=$NO_PROXY,cloud.science-it.uzh.ch
```
:::

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} Manual Inspection

```bash
# Retrieve the Swift endpoint catalog
openstack catalog show swift
# Generate a temporary access token
openstack token issue
```
::::
::::{grid-item-card} Automated Extraction

```bash
export RGW_URL=$(openstack catalog show swift -f json -c endpoints \
  | jq --raw-output '.endpoints[] | select(.interface=="public") | .url' \
  | head -n 1)
export RGW_TOKEN=$(openstack token issue -f value -c id)

```

::::
:::::

{% endif %}

{% if page %}
Interaction with OpenStack Object Storage (Swift or Ceph RGW) is fundamentally executed via RESTful API calls. To construct these requests, two critical pieces of information must be dynamically retrieved from the OpenStack environment: the public endpoint URL of the storage gateway and a valid authentication token.

### Endpoint Retrieval & Authentication

Assuming the terminal session is authenticated via `source project-openrc.sh`, the required parameters can be extracted utilizing the OpenStack CLI and JSON parsing utilities like `jq`.

* **Endpoint URL (`RGW_URL`):** The storage service exposes various endpoints (public, internal, admin). The public URL is extracted from the service catalog.
* **Authentication Token (`RGW_TOKEN`):** A temporary Keystone token must be generated to authorize the HTTP requests.

```bash
# Extract the public endpoint URL specifically
export RGW_URL=$(openstack catalog show swift -f json -c endpoints \
  | jq --raw-output '.endpoints[] | select(.interface=="public") | .url' \
  | head -n 1)

# Extract the temporary token string
export RGW_TOKEN=$(openstack token issue -f value -c id)

```

:::{admonition} Token Expiration
:class: warning
Authentication tokens issued by Keystone are **temporary** and typically expire after one hour. Once expired, API requests will return `401 Unauthorized` errors, necessitating the generation of a new token.
:::
{% endif %}

---

### Interface Methodologies

{% if slide %}
:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} Native Swift API (`curl`)
```bash
# List containers (buckets)
curl -i -X GET "${RGW_URL}" \
  -H "X-Auth-Token: ${RGW_TOKEN}"

# Upload an object
curl -i -X PUT "${RGW_URL}/bucket/file.txt" \
  -H "X-Auth-Token: ${RGW_TOKEN}" \
  --data-binary "@./file.txt"

# Delete an object
curl -i -X DELETE "${RGW_URL}/bucket/file.txt" \
  -H "X-Auth-Token: ${RGW_TOKEN}"

```
:::{admonition} Token Expiration
:class: warning
Keystone authentication tokens are **temporary** (typically expiring after 1 hour). The RC file must be re-sourced, or a new token issued, upon expiration.
:::

::::
::::{grid-item-card} S3 Compatibility API (`s3cmd`)
Utilizes the S3-compatible interface, requiring EC2 credentials.

```bash
# Generate S3-compatible credentials
ACCESS_KEY=$(openstack ec2 credentials create \
  -f value -c access)
SECRET_KEY=$(openstack ec2 credentials show \
  "${ACCESS_KEY}" -f value -c secret)

```

**Configuration (`~/.s3cfg`):**

```ini
[default]
host_base = ${RGW_HOST}
host_bucket = ${RGW_HOST}
access_key = ${ACCESS_KEY}
secret_key = ${SECRET_KEY}
use_https = True

```

::::
:::::

:::{admonition} Configuration Overhead Mitigation
:class: tip
To circumvent manual credential generation and configuration file management, the entire authentication and execution sequence can be encapsulated within an Apptainer orchestration image:
**[GitHub: pSciComp/s3cmdContainer](https://github.com/pSciComp/s3cmdContainer)**
:::
{% endif %}

{% if page %}
Once the endpoint architecture is understood, storage interaction can be facilitated via two primary methodologies: utilizing the native Swift API directly, or leveraging OpenStack's S3 compatibility layer with standard S3 tooling.

### Native Swift API (`curl`)

The most fundamental approach involves issuing raw HTTP requests directly to the Swift API utilizing `curl`. This method utilizes the temporary `X-Auth-Token` retrieved previously. While highly transparent and requiring no additional software dependencies, it becomes syntactically cumbersome for complex recursive operations or large file multipart uploads.

```bash
# List all containers (buckets) within the project
curl -i -X GET "${RGW_URL}" \
  -H "X-Auth-Token: ${RGW_TOKEN}"

# Upload a local file to a specific container
curl -i -X PUT "${RGW_URL}/mybucket/myfile.txt" \
  -H "X-Auth-Token: ${RGW_TOKEN}" \
  --data-binary "@./myfile.txt"

# Remove an object from the storage
curl -i -X DELETE "${RGW_URL}/mybucket/myfile.txt" \
  -H "X-Auth-Token: ${RGW_TOKEN}"

```

### S3 Compatibility API (`s3cmd`)

Modern OpenStack deployments provide an S3-compatible API layer. This allows the utilization of robust, established S3 clients such as `s3cmd`.

However, the S3 protocol does not utilize temporary Keystone tokens. Instead, it relies on static Access and Secret keys. To interface with the S3 compatibility layer, AWS EC2-style credentials must first be generated within the OpenStack project.

```bash
# Generate standard S3-compatible access credentials
ACCESS_KEY=$(openstack ec2 credentials create -f value -c access)
SECRET_KEY=$(openstack ec2 credentials show "${ACCESS_KEY}" -f value -c secret)

```

These credentials, along with the parsed host domain (the `RGW_URL` stripped of its `https://` prefix, denoted here as `RGW_HOST`), are then injected into the standard `s3cmd` configuration file (`~/.s3cfg`).

```ini
# ~/.s3cfg
[default]
host_base = ${RGW_HOST}
host_bucket = ${RGW_HOST}
access_key = ${ACCESS_KEY}
secret_key = ${SECRET_KEY}
use_https = True

```

Once configured, the storage can be interacted with using standard S3 commands (e.g., `s3cmd ls s3://mybucket`).

:::{admonition} Configuration Overhead Mitigation
:class: tip
The prerequisite steps of generating EC2 credentials, parsing endpoints, and configuring `.s3cfg` files introduce significant setup friction, particularly in automated CI/CD pipelines or reproducible compute jobs. To mitigate this configuration overhead, the entire authentication and `s3cmd` execution sequence can be encapsulated within an orchestration container.

A reference implementation of this containerized abstraction is available here:
**[GitHub: pSciComp/s3cmdContainer](https://github.com/pSciComp/s3cmdContainer)**
:::
{% endif %}
