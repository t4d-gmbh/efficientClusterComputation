(DisklessArchitecture)=
## "Diskless" Architecture

:::{admonition} Network-Dependent Architecture
:class: tip, margin
{% if slide %}
Compute nodes utilizing network-provisioned filesystems.
{% else %}
Compute nodes configured such that virtually all available filesystems are provided over the network. Local storage is unavailable for user data or temporary scratch space.
{% endif %}
:::
{% if slide %}

```{compound}
{.centered}
Centralized, network-dependent cluster architecture.


```

{% else %}

In certain High Performance Computing (HPC) environments, a setup is implemented wherein compute nodes provide no local storage to the user.
In this architecture, virtually all available filesystems—including standard paths such as `/home`, `/scratch`, and `/tmp`—are mapped to a persistent shared filesystem and provided over the network.

While hardware management is simplified, all user read and write operations are forced to traverse the high-speed network interconnect. As persistent storage tiers prioritize data safety and reliability over raw throughput, reliance on them for temporary, high I/O tasks introduces performance penalties.

{% endif %}

::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}
:columns: {% if page %}6{% else %}5{% endif %}
:class: sd-m-auto

{% if page %}

#### Architecture

Within this configuration, the compute node functions exclusively as a processing engine for user workloads.
Upon job initialization by the workload manager (e.g., Slurm), all file interactions, including the generation of intermediate temporary files, are written directly to the central storage cluster (e.g., CephFS) via the network.
{% else %}

#### Usage

* Standard file paths (`/home`, `/scratch`, `/tmp`, etc) map to the network.
* Data is accessible across nodes.
* I/O contention risk is increased.
* Mitigation strategies:
  * I/O reduction.
  * Utilization of alternative storage (Object storage, [RAM-based storage](https://www.google.com/search?q=%23inMemoryStorage)).
{% endif %}
:::
:::{grid-item}
:columns: {% if page %}6{% else %}7{% endif %}
:class: sd-m-auto



```{image} ./../_static/sharedFolders.png
:alt: sidebar image

```

:::
::::

{% if page %}

From a user perspective, the interface appears identical to a standard node, and standard shell commands operate as expected.
However, the operational mechanics differ.

Due to the absence of local scratch storage, workloads characterized by high-frequency read/write operations overload the metadata servers of the persistent storage.
This inefficiency degrades aggregate performance for concurrent cluster processes, a phenomenon termed the "Noisy Neighbor" effect.

:::{admonition} `tmpfs` Example
:class: margin tip
Utilization of RAM for low-latency temporary storage:

```bash
# Inside a Slurm script
cp my_heavy_dataset.zip /dev/shm/
cd /dev/shm/
unzip my_heavy_dataset.zip


```

:::

To manage I/O-intensive tasks without compromising shared filesystem stability, **[RAM-based temporary storage](https://www.google.com/search?q=%23inMemoryStorage)** can be utilized.
Linux environments provide a `tmpfs` mount, typically located at `/dev/shm`.
This operates as a local directory residing entirely within the node's random-access memory, circumventing network traversal.

System memory is a finite resource.
Data stored within `/dev/shm` is subtracted from the memory allocation available to the executed application.
Exceeding the remaining allocated memory limits results in process termination via an Out-Of-Memory (OOM) error.

{% endif %}

#### Use Cases

{% if slide %}
::::{grid} 1 3 3 3
:gutter: 1

:::{grid-item-card} CPU-Bound Operations
:::
:::{grid-item-card} Streamed Workloads
:::
:::{grid-item-card} In-Memory Staging
:::

::::

{% else %}

**CPU-Bound Operations**:

Executions that read initial configurations, process over extended durations, and write minimal output.

**Streamed Workloads**:

Data pipelines utilizing external libraries to stream data chunks directly from object storage into memory, bypassing temporary disk requirements.

**In-Memory Staging**:

Workflows designed with large memory allocations, where datasets are copied to `/dev/shm` for processing, followed by the transfer of output data to the shared filesystem prior to job termination.

#### System Attributes

{% endif %}

| Advantages | Limitations |
| --- | --- |
| **Hardware Reduction**{% if page %}:<br><br>Local storage devices are eliminated from the user environment, reducing potential points of hardware failure.{% endif %} | **Network Latency**{% if page %}:<br><br>All user storage I/O operations are transmitted via the network, increasing latency for storage operations.{% endif %} |
| **Data Centralization**{% if page %}:<br><br>All user data is centralized on redundant storage arrays.{% endif %} | **I/O Contention**{% if page %}:<br><br>Susceptibility to system-wide performance degradation caused by concurrent high-throughput network operations.{% endif %} |
| **Resource Allocation**{% if page %}:<br><br>Capital is reallocated from local storage devices to expanded computational resources.{% endif %} | **Memory Repurposing**{% if page %}:<br><br>System memory must be allocated for temporary file operations, reducing available memory for application processing.{% endif %} |
