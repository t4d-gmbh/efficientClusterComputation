## Concern Substitution

{% if slide %}
**Modular Runtime Substitution**
* The strict application of the Separation of Concerns (SoC) principle allows individual project components to be substituted at runtime without rebuilding the container image.

::::{grid} 2
:gutter: 2

:::{grid-item-card} Binding Data at Runtime
* **Lightweight Images:** Data directories bound dynamically (e.g., `--bind ./data/raw:/app/data/raw`).
* **Agnostic Versioning:** Host system manages data state (DVC / Git LFS) prior to execution.
:::
:::{grid-item-card} Dynamic Parametrization
* **Behavior Alteration:** Configurations bound dynamically (`--bind ./config:/app/config`).
* **HPC Integration:** Enables rapid parameter sweeps and embarrassingly parallel workloads (e.g., Slurm job arrays) without image modification.
:::
:::{grid-item-card} Containerized Development
* **Algorithmic Logic (`/src`):** Bind host source for live code testing (requires `pip install -e .` during build). Bypasses image rebuilds.
* **Orchestration (`/scripts`):** Bind scripts to dynamically modify pipeline and job submission logic.
:::
:::{grid-item-card} ⚠️ Development vs. Production
:class-header: bg-warning
* **Development Agility:** Extensive runtime binding enables rapid iterative testing.
* **Production Reproducibility:** Dynamic substitution violates strict artifact provenance.
* **Resolution:** Finalized codebase and configurations **must** be permanently compiled into the immutable production container.
:::
::::
{% else %}
The rigid application of the Separation of Concerns (SoC) principle—segregating configuration (`config/`), codebase (`src/`), and data (`data/`), and orchestrating them exclusively via minimal conductor scripts (`scripts/`)—enables the modular substitution of components at runtime. This modularity allows a single, immutable container image to be dynamically adapted for various execution contexts.

```{admonition} Container Structure Assumption
:class: note
The following architectural examples assume the container has been built with the project root mapped to an internal directory designated as `/app`.

```

### Binding Data at Runtime

By leveraging bind mounts at runtime (e.g., `--bind ./data/raw:/app/data/raw`), specific data directories are injected into the container's isolated filesystem. This architectural choice maintains a minimal container footprint, as massive datasets are not baked into the image. Furthermore, it allows the exact same analysis or training environment to be applied sequentially or concurrently to entirely different datasets.

**Integration with Data Versioning Tools:**
This substitution strategy pairs seamlessly with data management tools like Data Version Control (DVC) or Git Large File Storage (Git LFS).

* The host system assumes responsibility for data state management (e.g., executing `dvc pull` or `git lfs pull` to retrieve specific data versions from remote object storage).
* Once the data is materialized on the host's local filesystem, the directory is bound into the container.
* Consequently, the containerized application requires no knowledge of DVC or Git LFS internals; it simply reads from the standard POSIX filesystem path provided at runtime.

### Parameter Sweeps and Parallel Workloads

Mounting the configuration directory (e.g., `--bind ./config:/app/config`) permits the alteration of project parameters at runtime. Because the algorithmic logic (`src/`) is strictly separated from its parametrization, entirely different behaviors can be induced without modifying the container image or the underlying codebase.

This capability is critical for executing parameter sweeps or deploying embarrassingly parallel workloads across High-Performance Computing (HPC) clusters.

**Slurm Array Example:**
In an HPC context, a Slurm job array can be utilized to process hundreds of configurations simultaneously across multiple compute nodes. By mapping the Slurm Array Task ID to a specific configuration file on the host, each parallel task mounts its unique parametrization into the expected default location inside the container.

```bash
#!/bin/bash
#SBATCH --job-name=param_sweep
#SBATCH --output=logs/sweep_%A_%a.out
#SBATCH --array=1-100   # Spawn 100 parallel jobs
#SBATCH --nodes=1
#SBATCH --ntasks=1

# The host contains 100 distinct config files (e.g., config_1.yaml, config_2.yaml)
# The container expects the active config at /app/config/active.yaml

apptainer exec \
    --bind ./config/config_${SLURM_ARRAY_TASK_ID}.yaml:/app/config/active.yaml \
    my_project.sif \
    python /app/scripts/execute_pipeline.py

```

### Containerized Development

If the project package is installed in "editable" mode (e.g., utilizing `pip install -e .` or `uv pip install -e .` in the definition file) during the container's build phase, a highly efficient containerized development environment can be established.

By binding the host's local source code directory to the container's internal source directory at runtime (`--bind ./src:/app/src`), any modifications made to the codebase on the host are instantly reflected within the isolated execution context. This allows developers to write code on their local machine while executing and testing it against the exact, immutable software stack defined by the container, entirely circumventing the need for time-consuming image rebuilds.

### Application Orchestration Development

Extending the substitution pattern to the orchestration layer by binding the scripts directory (`--bind ./scripts:/app/scripts`) allows the conductor step to be altered dynamically. This is particularly useful when developing or debugging the high-level execution flow, data routing, or pipeline definitions. End-to-end pipeline development and testing can be conducted rapidly while ensuring the underlying algorithmic logic (`src/`) and environment stack remain pristine and reproducible.

:::{admonition} Warning: The Development vs. Production Dichotomy
:class: warning

While runtime substitution via bind mounting provides significant agility during active development, its application as a primary production architecture introduces critical vulnerabilities regarding computational reproducibility.

**Development Agility vs. Production Reproducibility**
Extensive runtime binding—such as injecting the codebase and configuration dynamically—facilitates rapid, iterative testing by circumventing the container build process. However, for production execution, benchmarking, or scientific archival, this dynamic substitution paradigm must be strictly avoided.

If the codebase and configuration are heavily externalized and only supplied at runtime, the container image ceases to function as a self-contained, immutable historical record. The chain of computational provenance is broken, as standard logging mechanisms capture the generic image but fail to record the precise state of the injected host directories.

**The Immutable Artifact Requirement**
To guarantee strict provenance and reproducibility, a distinct transition from development to production is required: the finalized, thoroughly tested codebase and definitive configurations must be permanently compiled ("baked") into the final container artifact. This ensures the resulting image serves as an indisputable, self-contained single source of truth for the executed computation, eliminating the risk of host-environment desynchronization.
:::
{% endif %}
