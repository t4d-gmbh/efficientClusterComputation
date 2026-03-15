{% if slide %}
## WIP: Accessing Object Storage

### Getting the Swift Endpoint & Token

Use the `openstack` CLI to retrieve the gateway URL and generate an access token:

```bash
# Get the Swift endpoint URL
openstack catalog show object-store

# Generate a temporary access token
openstack token issue
```

---

### RC File for Object Storage

Source an RC file that sets both the URL and token as environment variables:

```bash
# swift_rc.sh
export OS_STORAGE_URL=$(openstack catalog show object-store \
    -f value -c endpoints | grep public | awk '{print $2}')
export OS_AUTH_TOKEN=$(openstack token issue \
    -f value -c id)
```

```bash
source swift_rc.sh
```

:::{admonition} Token Lifetime
:class: warning
Tokens are **temporary** and expire (typically after a few hours).
Re-source the RC file or re-issue when needed.
:::

{% endif %}
