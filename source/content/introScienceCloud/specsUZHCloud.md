{% if slide %}
## UZH Science Cloud Overview

```{admonition} <i class="fa-solid fa-circle-info"></i> Full documentation
:class: margin tip
[docs.s3it.uzh.ch/cloud2](https://docs.s3it.uzh.ch/cloud2/overview/)
```

:::::{grid} 1 1 2 2
:gutter: 3

::::{grid-item-card} <i class="fa-solid fa-cubes"></i> Capacity
:class-header: sd-bg-primary sd-bg-text-light

OpenStack-based IaaS

^^^

- Managed by **S3IT** (UZH)
- OpenStack cloud OS
- **~10K vCPUs** / **~40 TB RAM** / **~200 GPUs**
- [Multiple **flavors** (VM sizes) available](https://herd.science-it.uzh.ch/avail-flavors/avail-public-flavors.html)

+++
<i class="fa-solid fa-link"></i> [Cloud Overview](https://docs.s3it.uzh.ch/cloud2/overview/)
::::

::::{grid-item-card} <i class="fa-solid fa-coins"></i> Approximate Costs
:class-header: sd-bg-info sd-bg-text-light

Costs are not public

^^^

+++
<i class="fa-solid fa-link"></i> [Info on costs (internal)](https://www.zi.uzh.ch/en/teaching-and-research/science-it/about/terms-conditions.html)
::::
:::::

---

### Access & Services

- **SSH** via UZH VPN to your VM (e.g. `ssh ubuntu@<floating-ip>`)
- **Web GUI**: [cloud.science-it.uzh.ch](https://cloud.science-it.uzh.ch/)
- **CLI**: `openstack` Python client
- Services include: **Compute (Nova)**, **Block Storage (Cinder)**, **Object Storage (Swift)**, **Networking (Neutron)**

:::{admonition} Object Storage
:class: note
The Science Cloud also provides **Swift Object Storage** as a service. We will look at this next.
:::

{% endif %}
