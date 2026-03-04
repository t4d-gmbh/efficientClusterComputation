(recapSoC)=
# Separation of Concerns (recap)


::::::{grid} 1 2 2 2
:gutter: 2

:::::{grid-item}
:class: sd-m-auto
:columns: {% if page %}6{% else %}4{% endif %}

{% if slide %}
::::{grid} 2 2 2 2
:::{grid-item}

**Environment**:
- `containers/`
- `.env`  
:::
:::{grid-item}

**Configuration**:
- `config/`  
:::
:::{grid-item}

**Code**:
- `/src`
- `/scripts`  
:::
:::{grid-item}

**Data**:
- `data/`
- `data/README.md`
:::
::::
{% else %}
Computational reproducibility can be drastically facilitated by the rigid Separation of Concerns (SoC) across four distinct, isolated pillars.
Failure to isolate these concerns can lead to security vulnerabilities (credentials in Git), rigid codebases, non-reproducible environments, and data integrity failures.
{% endif %}
:::::
:::::{grid-item}
:columns: {% if page %}6{% else %}8{% endif %}
:class: sd-m-auto

```{figure} ./../_static/fourPillarsSoC.png
:alt: 4 Pillars of SoC
:width: 100%
```
:::::
::::::

{% if page %}


Effective data handling is critical for computational efficiency, reproducibility, and scientific integrity.
Within a modern computational project, data must not be treated as an ephemeral byproduct but as a first-class citizen governed by a deliberate strategy.

This strategy hinges on the strict application of the **Separation of Concerns (SoC)** principle.
Concerns are separated into four distinct pillars, which only intersect at the moment of execution within a controlled script:

1. **Environment** (Runtime context, secrets, and dynamic paths)
2. **Configuration** (Static algorithmic parameters)
3. **Code** (Reusable logic vs. execution scripts)
4. **Data** (Declarations of input state and output artifacts)


#### Optimized Project Root Structure

To physically enforce SoC across all four pillars, the project root must adhere to a standardized scaffolding.

```text
Project Root/
├── .git/
├── .env               # Pillar 1 (Ignored): Local secrets/paths
├── .env.example       # Pillar 1 (Committed): Env template
├── pyproject.toml     # Pillar 1 (Committed): Dependency declaration
├── containers/        # Pillar 1 (Committed): Singularity/Docker definitions
│   └── Singularity.def
├── config/            # Pillar 2 (Committed): Static Parameters
│   └── train_config.yml (NO secrets, NO paths)
├── src/               # Pillar 3 (Committed): Reusable, independent logic
│   └── model/
├── scripts/           # Pillar 3 (Committed): Intersection/Execution scripts
│   └── run_training.py (Combines Env+Config+Src+Data)
└── data/              # Pillar 4 (Excluded/Pointers): State Declarations
    ├── raw/           # Immutable original inputs
    ├── interim/       # Intermediate transformed artifacts.
    └── final/         # Processed datasets, models, and final results.

```

{% endif %}
