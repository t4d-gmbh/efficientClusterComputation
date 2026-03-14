# Example `.def` #3: Full SoC Workflow

The complete call with the container follows the SoC pattern:

```bash
apptainer exec --pwd /app \
               --env-file .env \
               --bind "$(pwd):/app" \
               env.sif \
               python scripts/say_hello.py
```

:::::{grid} 2
:gutter: 2

::::{grid-item-card} How the 4 Pillars Map
| SoC Pillar | Where |
|---|---|
| **Environment** | `.env` + container (`env.sif`) |
| **Configuration** | `config/hello_to.json` (in image or bound) |
| **Code** | `src/exohw/` baked into the image |
| **Data** | `data/` bound from host via `--bind` |
::::

::::{grid-item-card} From Registry
Skip the local build entirely:
```bash
apptainer run \
  oras://ghcr.io/pscicomp/env-sif:latest \
  python scripts/say_hello.py
```

Container built via CI/CD from the `.def` tracked in the repo.
::::
:::::

:::{admonition} Takeaway
:class: tip
The `.def` is **version-controlled** alongside the code. The `.sif` binary is **not tracked** — it is either built locally or pulled from a registry. This keeps the environment reproducible without bloating the repository.
:::
