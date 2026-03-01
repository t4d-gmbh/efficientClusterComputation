{% if build == "slides" %}
# The HPC Cluster
{% else %}
## The HPC Cluster at a Glance
{% endif %}

```{figure} ./../_static/slurmCluster.png
:alt: Slurm Cluster Schema
:width: 100%
```

{% if page %}

An HPC Cluster aggregates individual servers into a single computing environment.
Key components: **Login Nodes** (gateway), **Workload Manager / Slurm** (scheduler), **Compute Nodes** (CPUs/GPUs), **Shared Storage** (parallel filesystem), and the **Interconnect** (high-speed network fabric).

{% endif %}


