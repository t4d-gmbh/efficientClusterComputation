## Binds at Runtime

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class-header: sd-bg-primary sd-bg-text-light

**What `--bind` mounts were needed to run the script through the container?**
:::


:::{admonition} Host's `PATH` is appended on runtime
:class: note

Apptainer will append the value of `PATH` of the host to `PATH` inside the container.

Running `module load ...` on the host, `PATH` might contain references to `/apps/...`.
If then `/apps` is bound, the **container might run binaries from the host** and not the container.

{.centered}
**This is needed if efficient usage of the network fabric is required** (e.g. RDMA).
:::

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-gear"></i> Typical binds

```bash
--bind "$(pwd)/data:/app/data"
```
Mounts the project's data folder into `/app/data` so the container can read and write `data/`.

Additional binds for HPC:
```bash
--bind /scratch/<user>/results:data/final
```
::::
::::{grid-item-card} <i class="fas fa-circle-info"></i> Control runtime binds
Avoid unnecessary binds (e.g. `/home`)

Only bind input (config, data) and output (data) directories.

Bind system libraries and executables (e.g. `/apps`) **only if needed**!

:::{admonition} Binds overwrite content!
:class: warning

This will overwrite the `/app` folder inside the container (e.g. `app/src/`).
```bash
--bind "$(pwd):/app"
```
:::
::::
:::::
