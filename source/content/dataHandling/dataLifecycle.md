# Computational Workflow and Data Lifecycle

{% if slide %}
::::{grid} 1 3 3 3
:gutter: 2

:::{grid-item-card} Setup & Prep
:class-header: sd-bg-primary sd-bg-text-light

Prolog

^^^

* Localize declared data
* Declare required environment variables
* Instantiation of the controlled environment
:::
:::{grid-item-card} Process
:class-header: sd-bg-info sd-bg-text-light

Execution

^^^

* Parse environment variables
* Load static configurations
* Perform data preparation (extraction, preprocessing) on ephemeral scratch tier
* Apply reusable logic

:::
:::{grid-item-card} Persistence
:class-header: sd-bg-warning sd-bg-text-light

Epilog

^^^

* Persist computed data artifacts
* Transfer results to persistend storage using env credentials
* System/scratch purge
:::
::::
{% else %}

Data movement and job execution transitioning between persistent external storage (Object Storage) and volatile performance tiers (Ephemeral Scratch) occurs strictly within the coordinated interaction of the four pillars.

| Stage | Action | Pillar Interaction Description |
| --- | --- | --- |
| **1. Prolog** | **Setup & Prep** | Necessary dynamic **Environment** variables (Pillar 1) are declared, and the controlled environment is instantiated (e.g., container boot, configuring network access, and loading secrets from `.env` files). Declared **Data** sources (Pillar 4) are then localized (staged-in) from persistent external storage to the volatile performance tier (Ephemeral Scratch) using dynamic **Environment** paths and credentials. |
| **2. Execution** | **Process** | The execution **Script** (Pillar 3) serves as the "conductor." It first parses the declared **Environment** variables (Pillar 1) and loads the static algorithmic **Configuration** (Pillar 2) parameters. Necessary data preparation steps, such as extracting archives or performing initial preprocessing, are executed directly on the low-latency scratch tier. Finally, the script imports reusable algorithmic **Logic** (Pillar 3) and applies it to the prepared data in total isolation from the environment context. |
| **3. Epilog** | **Persistence** | Computed **Data** artifacts (Pillar 4) and valuable results are persisted and transferred back to persistent storage tiers (e.g., Object Storage or a persistent shared filesystem) using **Environment** (Pillar 1) credentials and staging-out tools *before* the temporary execution environment and volatile scratch storage are purged. |
{% endif %}
