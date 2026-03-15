{% if slide %}
## Object Storage Clients

All you need is the **URL** and a valid **token**.

### Available Clients

:::::{grid} 2 2 3 3
:gutter: 2

::::{grid-item-card} `rclone`
:class: sd-shadow-s
Sync-style tool for cloud storage.
::::
::::{grid-item-card} `mc` (MinIO)
:class: sd-shadow-s
S3-compatible CLI client.
::::
::::{grid-item-card} `boto3`
:class: sd-shadow-s
Python SDK for S3/Swift.
::::
::::{grid-item-card} `pandas`
:class: sd-shadow-s
Read CSV/Parquet directly from object storage.
::::
::::{grid-item-card} `curl`
:class: sd-shadow-s
HTTP client — works everywhere.
::::
:::::

---

### Basic Operations with `curl`

```bash
# List containers
curl -s -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     "$OS_STORAGE_URL"

# Upload a file
curl -X PUT \
     -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     -T myfile.txt \
     "$OS_STORAGE_URL/mycontainer/myfile.txt"

# Download a file
curl -s -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     -o myfile.txt \
     "$OS_STORAGE_URL/mycontainer/myfile.txt"

# Delete a file
curl -X DELETE \
     -H "X-Auth-Token: $OS_AUTH_TOKEN" \
     "$OS_STORAGE_URL/mycontainer/myfile.txt"
```

:::{admonition} We will use `curl` in the exercises
:class: tip
These basic HTTP operations are all you need to interact with object storage programmatically.
:::

{% endif %}
