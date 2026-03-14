## What needs to be build inside?

**What commands need to be put into `%post`?**

:::{admonition} Setup, install & cleanup
:class: note

This is the **only** stage that allows you to **modify** the content of the container.
:::


:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-gear"></i> Typically Contains

- **Installation of system level dependencies**.
- Add needed **tools to build, install and execute** the project.
- Perform the actual **installation of the project**.
- **Remove all unused tools and content** again.
::::

::::{grid-item-card} <i class="fas fa-terminal"></i> Exemplary Commands
```bash
# dependencies
apt-get update && apt-get install -y curl git
# build tools
curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/bin" sh
# install package
cd /app
uv sync
# cleaning up
uv cache clean
rm -rf .git
```
::::
:::::

