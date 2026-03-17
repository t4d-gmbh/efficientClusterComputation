{% if slide %}
## Object Storage Clients

Interaction with object storage requires the endpoint URL and valid authentication credentials.

### Available Interfacing Tools

:::::{grid} 2 2 3 3
:gutter: 2

::::{grid-item-card} `rclone`
:class: sd-shadow-s
Synchronization utility for cloud storage architectures.
::::
::::{grid-item-card} `mc` (MinIO)
:class: sd-shadow-s
Modern, S3-compatible command-line client.
::::
::::{grid-item-card} `s3cmd`
:class: sd-shadow-s
Established CLI client for S3-compatible endpoints.
::::
::::{grid-item-card} `boto3`
:class: sd-shadow-s
Python SDK for programmatic S3/Swift integration.
::::
::::{grid-item-card} `pandas`
:class: sd-shadow-s
Direct ingestion of CSV/Parquet from object storage.
::::
::::{grid-item-card} `curl`
:class: sd-shadow-s
Universal HTTP client for direct REST API calls.
::::
:::::

---

::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item-card} Basic Operations with `curl`

```bash
# List containers (buckets)
curl -s -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     "$OS_STORAGE_URL"

# Upload an object
curl -X PUT \
     -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     -T myfile.txt \
     "$OS_STORAGE_URL/mycontainer/myfile.txt"

# Delete an object
curl -X DELETE \
     -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     "$OS_STORAGE_URL/mycontainer/myfile.txt"

```

:::
:::{grid-item-card} Basic Operations with `s3cmd(c)`

```bash
# List containers (buckets)
s3cmdc ls

# Upload an object
s3cmdc put myfile.txt s3://mycontainer/myfile.txt

# Download an object
s3cmdc get s3://mycontainer/myfile.txt myfile.txt

# Delete an object
s3cmdc del s3://mycontainer/myfile.txt

```

:::
::::

:::{admonition} Exercise Execution Paths
:class: tip
For the upcoming exercises, two supported execution paths are recommended:

1. **Direct API calls:** Utilizing bare `curl` to execute the fundamental HTTP operations outlined above.
2. **Containerized tooling:** Utilizing the pre-configured `s3cmd` container abstraction ( [`s3cmdContainer`](https://github.com/pSciComp/s3cmdContainer) / `s3cmdc`) to simplify command syntax and bypass manual S3 configuration overhead.
:::

{% else %}

Interfacing with OpenStack object storage infrastructure is accomplished via two primary protocols: the native OpenStack Swift REST API or the Amazon S3-compatible API (typically provided via Ceph RADOS Gateway or Swift3 middleware). The selection of the interface dictates the required tooling and authentication mechanisms.

### Available Interfacing Tools

A variety of open-source utilities and libraries are available, ranging from low-level HTTP clients to high-level data synchronization frameworks:

* **Command-Line Interfaces (CLIs):**
  * **`curl`:** The universal standard for raw HTTP communication. It interacts directly with the native Swift REST API utilizing temporary Keystone tokens. It requires no additional dependencies, making it ideal for minimal, reproducible shell scripts.
  * **`s3cmd` & `mc` (MinIO Client):** Dedicated CLI utilities designed for S3-compatible endpoints. They require EC2-style static credentials (Access/Secret keys) rather than temporary tokens and utilize the standard `s3://` URI scheme.
  * **`rclone`:** A robust synchronization utility that supports multiple cloud storage backends. It is optimized for large-scale data transfers, directory mirroring, and validating data integrity via checksums.
* **Programmatic Integration:**
  * **`boto3`:** The standard Python SDK for S3. It allows storage operations to be embedded directly within analytical pipelines or application code.
  * **Data Science Libraries (e.g., `pandas`):** High-level libraries often support direct ingestion of remote data. For instance, a CSV or Parquet file can be read directly into a DataFrame from an S3 endpoint by passing the appropriate storage options and URI, bypassing local intermediate storage.

### Exercise Execution Paths

For execution and automation within reproducible environments, two distinct methodologies are highlighted:

#### 1. Direct API Execution (`curl`)
This approach leverages the native Swift API. Operations are executed as standard HTTP requests (`GET`, `PUT`, `DELETE`). Authentication is handled by passing the temporary OpenStack token (`$OS_AUTH_TOKEN`) via the `X-Auth-Token` HTTP header. 

The target resource is identified by appending the container name and object key directly to the storage endpoint URL (e.g., `"$OS_STORAGE_URL/mycontainer/myfile.txt"`). This method guarantees execution capability on any standard POSIX system without installing specialized client software.

#### 2. Containerized S3 Compatibility (`s3cmdc`)
When the syntactic simplicity of the S3 protocol is preferred, the `s3cmd` utility is utilized. To maintain environmental purity and avoid global package installations on the host system, a containerized abstraction of `s3cmd` (referred to here as `s3cmdc`) is recommended. 

This container encapsulates the software dependencies and the `.s3cfg` configuration file, allowing standard S3 commands (e.g., `s3cmdc put local_file s3://bucket/remote_file`) to be executed dynamically while strictly adhering to the principles of computational reproducibility.
{% endif %}
