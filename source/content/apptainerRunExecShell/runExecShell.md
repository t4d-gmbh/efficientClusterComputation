# `run` vs `exec` vs `shell`

:::::{tab-set}

::::{tab-item} <i class="fas fa-terminal"></i> shell
**Interactive session**

```bash
apptainer shell env.sif
```

Drops into a shell inside the container. Useful for exploring, debugging, and testing commands interactively.

*Use during development.*
::::

::::{tab-item} <i class="fas fa-play"></i> exec
**Run a specific command**

```bash
apptainer exec env.sif \
  python scripts/say_hello.py
```

Executes exactly one command and exits. Good for ad-hoc tasks or when the image has no `%runscript`.

*Use for one-off commands.*
::::

::::{tab-item} <i class="fas fa-rocket"></i> run
**Execute the `%runscript`**

```bash
apptainer run env.sif \
  python scripts/say_hello.py
```

Triggers the `%runscript` defined in the `.def` file. The container author controls the entry point and working directory.

*Use for reproducible batch execution.*
::::
:::::

---

:::{admonition} <i class="fas fa-star"></i> Prefer `run` for reproducibility
:class: tip

`run` executes the `%runscript`, which is baked into the image at build time. This means the working directory, environment setup, and entry logic are fixed by the container author — not left to the caller. For batch jobs and Slurm submissions, this makes execution deterministic and self-documenting.
:::
