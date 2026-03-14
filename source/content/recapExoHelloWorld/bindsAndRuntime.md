# Binds & Runtime

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class: attention

**What `--bind` mounts were needed to run the script through the container?**
:::

---

:::::{grid} 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-link"></i> Typical Binds
```bash
--bind "$(pwd):/app"
```
Mounts the project root into `/app` so the container can access `data/`, `config/`, and `scripts/` from the host.

Additional binds for HPC:
```bash
--bind /scratch/<user>/results:data/final
```
::::

::::{grid-item-card} <i class="fas fa-terminal"></i> `run` vs `exec` vs `shell`

| Mode | Use Case |
|---|---|
| `shell` | Interactive exploration & debugging |
| `exec` | Run a specific command or script |
| `run` | Execute the `%runscript` — **best for reproducibility** |

```bash
# Recommended for batch jobs
apptainer run --env-file .env \
  --bind "$(pwd):/app" \
  env.sif \
  python scripts/say_hello.py
```
::::
:::::
