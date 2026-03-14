# Example `.def` #2: With Config & Scripts

```docker
Bootstrap: docker
From: python:3.13-slim

%files
    pyproject.toml /app/
    LICENSE /app/
    README.md /app/
    src /app/src
    scripts /app/scripts
    config /app/config
    .git /app/.git

%post
    apt-get update && apt-get install -y curl git
    rm -rf /var/lib/apt/lists/*
    curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/bin" sh
    cd /app
    export UV_LINK_MODE=copy VIRTUAL_ENV=/opt/venv
    export PATH="/opt/venv/bin:$PATH" UV_PROJECT_ENVIRONMENT="/opt/venv"
    uv venv /opt/venv
    uv pip install --no-cache --compile-bytecode .
    rm -rf .git && uv cache clean

%environment
    export VIRTUAL_ENV=/opt/venv
    export PATH="/opt/venv/bin:$PATH"
    export UV_PROJECT_ENVIRONMENT="/opt/venv"
    export APPTAINER_PWD=/app SINGULARITY_PWD=/app
    export UV_NO_SYNC=1 UV_FROZEN=1

%runscript
    cd /app
    exec "$@"
```

Now `config/` and `scripts/` are part of the image. The `%runscript` sets `/app` as the working directory and forwards arguments.
