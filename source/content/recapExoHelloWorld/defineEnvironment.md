## How to prepare the runtime environment?

**What needs to go into `%environment`?**

:::{admonition} Environment variable handling
:class: note

Runtime environment variables are imported whenever the container runs.

Variables defined in `%environment` are **ignored during container build**!

:::

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-arrow-down-short-wide"></i> Runtime Environment Variables


- **Host Environment**: Inherited by default (unless `--cleanenv` is used).

- **`%environment` Section**: The internal image script (/.singularity.d/env/90-environment.sh) is _sourced_, potentially overwriting host variables.

- **`--env-file` option**: The file provided at runtime is sourced.
  Any variables here that share names with `%environment` variables will overwrite the internal image values.

- **`--env` / `APPTAINERENV_`**: Individual flags or prefixed host variables are applied last.
::::

::::{grid-item-card} <i class="fas fa-caret-right"></i> Typical Usage
```bash
%environment
    # Make sure uv does not try to edit the immutable content
    export UV_NO_SYNC=1
    # inserting executable paths
    export PATH="/opt/venv/bin:$PATH"
    # take full control over global search paths
    export PATH="/opt/venv/bin:/usr/local/bin:..."
```
::::
:::::
