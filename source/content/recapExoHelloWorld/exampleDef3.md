# Example `.def` #3: 

```docker
Bootstrap: docker
From: python:3.13-slim

%files
    pyproject.toml /app/
    LICENSE /app/
    README.md /app/
    .env /app/.env
    config /app/config
    data /app/data
    src /app/src
    scripts /app/scripts
%post
    cd /app
    pip install uv
    rm -rf /app/.venv
    uv sync
%environment
    export UV_ENV_FILE=/app/.env
    export UV_NO_SYNC=1
    export UV_FROZEN=1
    export PATH=/app/.venv/bin:$PATH
%runscript
    cd /app
    exec uv run --env-file .env scripts/say_hello.py
```