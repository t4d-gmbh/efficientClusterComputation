---
sd_hide_title: true
---
## The Definition File

{% if slide %}
```docker
# containers/env.def
Bootstrap: docker 
From: python:3.13-slim

%files 
    pyproject.toml /app/
    src /app/src
    ...
    # uv.lock /app/uv.lock 
 
%post 
    # System updates, install uv and system dependencies
    ...

    # Move into the app directory 
    cd /app 
 
    # Use environment variables to set the python env location (internally)
    export VIRTUAL_ENV=/opt/venv 
    ...
 
    # Initialize the venv and install dependencies directly from pyproject.toml 
    uv venv /opt/venv && uv pip install --no-cache --compile-bytecode .
    # uv sync --frozen --no-dev
 
%environment 
    # Ensure the container always activates this environment when shelling in 
    ...
#%runscript
    # exec python "$@" 
```
{% endif %}

{% if page %}
For projects that strictly adhere to the Separation of Concerns (SoC) principle, an "invisible" container can serve as the standardized execution environment for all scripts.
In this architectural pattern, the source code and configuration files remain on the host filesystem, while the container strictly provides the software dependencies and the isolated execution context.

```docker
Bootstrap: docker 
From: python:3.13-slim

%files 
    # Copy dependency files from the host into the container 
    pyproject.toml /app/
    src /app/src
    ...
    # uv.lock /app/uv.lock 
 
%post 
    # System updates, install uv and system dependencies
    ...

    # Move into the app directory 
    cd /app 
 
    # Inject the version and suppress filesystem warnings
    export VIRTUAL_ENV=/opt/venv 
    ...
 
    # Initialize the venv and install dependencies directly from pyproject.toml 
    uv venv /opt/venv && uv pip install --no-cache --compile-bytecode .
    # uv sync --frozen --no-dev
 
%environment 
    # Ensure the container always activates this environment when shelling in 
    ...
#%runscript
    # exec python "$@" 
```

This approach allows the container to be invoked seamlessly for both interactive development sessions and automated batch processing:

```bash
# Interactive shell with environment variables loaded
apptainer shell --env-file .env env.sif

# Batch execution of a specific script
apptainer exec --pwd /app \
               --env-file .env \
               --bind "$(pwd):/app" \
               containers/env.sif \
               python scripts/my_script.py

```

**Understanding the Execution Flags:**

* `--pwd /app`: Sets the initial working directory inside the container upon execution.
* `--env-file .env`: Injects environment variables (like API keys or configuration toggles) from a local `.env` file directly into the container.
* `--bind "$(pwd):/app"`: Mounts the current host directory into the container's `/app` path.
  If run from the root of your SoC project this provides access to the `data/`, directory.

A complete, production-ready example of such an "invisible" container definition can be found in the [T4D pythonProject template](https://github.com/j-i-l/pythonProject/blob/997c7e278ab02c02d7b861444a281d8364a3f1c7/containers/env.def).
Furthermore, the template provides a fully configured [CI/CD workflow](https://github.com/j-i-l/pythonProject/blob/997c7e278ab02c02d7b861444a281d8364a3f1c7/.github/workflows/buildApptainerEnv.yml) that demonstrates how to automatically build the Apptainer image via GitHub Actions and publish it directly to a container registry.

{% endif %}

:::{admonition} Invisible Container Template
:class: tip

An exemplary definition of such an "Invisible" container overlay can be found in [T4D's pythonProject template](https://github.com/j-i-l/pythonProject/blob/997c7e278ab02c02d7b861444a281d8364a3f1c7/containers/env.def) along with [a CI/CD workflow](https://github.com/j-i-l/pythonProject/blob/997c7e278ab02c02d7b861444a281d8364a3f1c7/.github/workflows/buildApptainerEnv.yml) to build and add the container to the GitHub registry.
:::


## Using the Environment
{% if slide %}

:::::{grid} 1 2 2 2
:gutter: 3

::::{grid-item}
:class: sd-m-auto

In a project adhering to the **Separation of Concerns** (SoC) this container can be **used for all scripts**, following the general call signature:

::::
::::{grid-item}
:class: sd-m-auto

```bash
apptainer exec --pwd /app \
               --env-file .env \
               --bind "$(pwd):/app" \
               containers/env.sif \
               python scripts/my_script.py

```
::::
:::::

:::{admonition} Fetch from registry
:class: note 
```bash
apptainer shell oras://ghcr.io/<owner>/<env-sif>:1.0.1
```
:::

{% else %}

To build this container from the root of a SoC project:

```bash
apptainer build env.sif containers/enf.def

```

Once built, an interactive shell can be launched.
The working directory remains the same as the host, but the dependencies are provided by the container.

```bash
# Drop into the interactive environment
$ apptainer shell env.sif

# The session remains in the project directory on the host
Apptainer> pwd
/scratch/user/my_project

# Python is now utilizing the dependencies baked into the .sif file
Apptainer> which python
/opt/venv/bin/python

# Execute scripts natively
Apptainer> python scripts/my_script.py

```

{% endif %}
