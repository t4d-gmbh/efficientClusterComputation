## What goes into the container?

**Which project artifacts ended up in `%files`?**

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-file-import"></i> Typically Included
| Artifact | Purpose |
|---|---|
| `pyproject.toml` | Dependency & build specification |
| `src/` | Reusable package code (`exohw`) |
| `.git/` | Version extraction via `hatch-vcs` |
| `LICENSE`, `README.md` | Required by build system |
::::

::::{grid-item-card} <i class="fas fa-file-export"></i> Optionally Included
| Artifact | Reasoning |
|---|---|
| `config/` | Fixed configuration can live inside |
| `scripts/` | Execution scripts as part of the image |
| `data/` | Only if small and static |
| `uv.lock` | Exact dependency pinning |
::::
:::::

---

:::{admonition} Key Insight
:class: tip
With **Separation of Concerns**, the container primarily packages the runtime **environment** (Python + dependencies) and the **code** (`src/`).
Configuration and data can be injected at runtime via `--env-file` and `--bind`.
:::
