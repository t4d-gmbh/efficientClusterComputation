# Suggested container definition 


```bash
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

    export VIRTUAL_ENV=/opt/venv 
    export PATH="/opt/venv/bin:$PATH" 
    export UV_PROJECT_ENVIRONMENT="/opt/venv"
    export UV_LINK_MODE=copy

    uv venv /opt/venv
 
    uv pip install --no-cache --compile-bytecode .
 
    rm -rf .git
    uv cache clean 

%environment
    export VIRTUAL_ENV=/opt/venv
    export UV_PROJECT_ENVIRONMENT="/opt/venv"
    export PATH="/opt/venv/bin:$PATH"
    export APPTAINER_PWD=/app
    export SINGULARITY_PWD=/app
    export UV_NO_SYNC=1
    export UV_FROZEN=1

%runscript
    cd /app
    exec "$@"
```

