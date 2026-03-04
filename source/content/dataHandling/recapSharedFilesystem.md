(DataHandlingStrategy)=
## Data Handling Strategy in HPC

:::{admonition} Data Staging
:class: tip, margin
{% if slide %}
Move data to fast storage before computation.
{% else %}
Computational efficiency and reproducibility rely on deliberate data staging: the programmatic transfer of datasets from persistent, slow tiers to volatile, high-speed tiers prior to execution.
{% endif %}
:::
{% if slide %}

```{compound}
{.centered}
Strategic utilization of the storage hierarchy is required to prevent systemic bottlenecks.

```

{% else %}

In distributed computing environments orchestrated by workload managers such as Slurm, data localization and Input/Output (I/O) efficiency dictate overall system performance.
Improper storage utilization introduces metadata bottlenecks and network saturation, degrading cluster efficiency through the "Noisy Neighbor" effect.
A structured approach to data handling is required to ensure reproducibility and optimize resource allocation.

{% endif %}

::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}
:columns: {% if page %}6{% else %}5{% endif %}
:class: sd-m-auto


{% if page %}

#### The Storage Hierarchy

Storage within an HPC cluster is not monolithic; it is partitioned into specific tiers engineered for distinct operational phases.

1. **Persistent Shared Filesystems**: Utilized for source code, environmental configurations (e.g., `.bashrc`), and compiled binaries. Fully POSIX-compliant, ensuring synchronization across login and compute nodes.
2. **Object Storage**: Employed as the primary data lake. Raw datasets and model artifacts are stored immutably and accessed via HTTP REST APIs (e.g., S3 protocols via Ceph RGW).
3. **Ephemeral Storage**: Designated for active computation. High-throughput, volatile storage (local NVMe or distributed scratch) utilized strictly during job execution.

{% else %}

#### Principles of Data Handling

* **Hierarchy Utilization**: Match storage tier to the data lifecycle phase.
* **Data Staging**: Transfer data to ephemeral tiers before processing.
* **I/O Optimization**: Minimize random read/write operations on network filesystems.
* **Reproducibility**: Script all data movements within the job submission.

{% endif %}
:::
:::{grid-item}
:columns: {% if page %}6{% else %}7{% endif %}
:class: sd-m-auto


```{image} ./../_static/dataHandling.png
:alt: sidebar image

```

:::
::::

{% if page %}

#### Data Staging and Reproducibility

To maximize compute node utilization (CPUs/GPUs), data must be staged.
Data staging is the methodology of moving required datasets into Ephemeral Storage prior to initiating the primary computational task.
For verifiable and reproducible research, this movement is codified directly within the job script (e.g., using open-source tools like `rsync` or `mc`).

1. **Prolog (Staging In)**: Data is pulled from Object Storage or Persistent Shared Filesystems into `$TMPDIR`.
2. **Execution**: The application performs heavy I/O operations exclusively against the local, low-latency ephemeral tier.
3. **Epilog (Staging Out)**: Computed results and artifacts are pushed back to a persistent tier (Object Storage or Shared Filesystem) before the automated purge policy destroys the ephemeral directory.

#### I/O Pattern Optimization

Network-attached filesystems are vulnerable to metadata exhaustion.
Workloads characterized by thousands of concurrent, small random read/write operations (e.g., processing millions of individual image files or SQLite transactions) must not be executed directly against Persistent Shared Filesystems.
If Ephemeral Storage is unavailable, datasets are aggregated into sequential formats (e.g., HDF5, Parquet) or processed in-memory.

{% endif %}
