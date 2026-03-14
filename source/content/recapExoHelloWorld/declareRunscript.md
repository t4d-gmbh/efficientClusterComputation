## What should the container do?

**How to use the `%runscript` section?**

:::{admonition} Turn your container into an application
:class: tip

A container is **not just an isolated environment**, it can contain a fully fledged script for execution.

For reproducibility **relying on `%runscript` is the recommended way** to work with containers.
:::

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-gears"></i> Functionality

**`apptainer run`**:  
This is the **only runtime command that does not ignore the `%runscript`** section.

Allows to define **immutable executions**:

```bash
%runscript
    /opt/venv/bin/python -m benchmark.run --iterations 100 --seed 42
```

The **"primary" application** pattern:
```bash
%runscript
    exec "$@"
    # exec python "$@"
```
_Lead to PID=1 and thus Unix signals to the container are forwarded directly, e.g. SIGTERM._

::::

::::{grid-item-card} <i class="fas fa-triangle-exclamation"></i> Pitfalls
`%runscript` is **ignored by `apptainer shell` and `apptainer exec`**.

**Omitting `exec` inside `%runscript`** associates PID=1 to the shell inside the container.
If the shell does not forward Unix signals (e.g. `/bin/sh` does not) any **child process might continue as orphaned process**.

Inside `%runscirpt`, **only write operations to bound host paths are possible**.

Environment variable modifications inside `%runscript` **overwrite all other variable declarations**.
::::
:::::
