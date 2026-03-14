# Example `.def` #1: Minimal

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class: attention
**Let's walk through some definitions — what do you notice?**
:::

```docker
Bootstrap: docker
From: python:3.13-slim

%files
    pyproject.toml /app/
    src /app/src
    .git /app/.git

%post
    apt-get update && apt-get install -y curl git
    rm -rf /var/lib/apt/lists/*
    curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/bin" sh
    cd /app
    uv venv /opt/venv
    export VIRTUAL_ENV=/opt/venv PATH="/opt/venv/bin:$PATH"
    export UV_PROJECT_ENVIRONMENT="/opt/venv" UV_LINK_MODE=copy
    uv pip install --no-cache --compile-bytecode .
    rm -rf .git

%environment
    export VIRTUAL_ENV=/opt/venv
    export PATH="/opt/venv/bin:$PATH"
```

Only `pyproject.toml`, `src/`, and `.git/` are baked in. Everything else comes from the host at runtime.
