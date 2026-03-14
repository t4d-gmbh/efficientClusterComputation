# Building & Running Apptainer

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class: attention

**Who managed to build and run Apptainer locally?**

- With or without help?
- Any issues encountered during setup?
:::

---

:::::{grid} 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-check-circle"></i> Expected Workflow
```bash
# Build from the project root
apptainer build env.sif containers/env.def

# Verify it works
apptainer shell env.sif
```
::::

::::{grid-item-card} <i class="fas fa-exclamation-triangle"></i> Common Pitfalls
- `fakeroot` failures → use `--ignore-fakeroot-command`
- `uv` tries to sync on runtime → set `UV_NO_SYNC=1`
- Missing `.git/` → version extraction fails during build
::::
:::::
