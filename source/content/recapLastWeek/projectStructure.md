## Project Root Structure

{.centered}
Keep it **persistent** across all your projects -> fast onboarding, zero guesswork.

```{admonition} Golden rule
:class: tip margin

The root of a repository should **always look the same**.
```

```bash
myProject/
│
├── .env               # Local paths & secrets (never committed!)
├── config/            # Parameters (YAML/TOML)
│
├── src/               # Reusable logic (Python package)
├── scripts/           # Executable entry points
├── containers/        # Dockerfiles, Apptainer defs
│
├── data/              # raw / interim / final
├── results/           # Figures, tables, models
│
├── pyproject.toml     # Dependencies & metadata
├── README.md          # Project overview
└── .gitignore         # VCS exclusions
```