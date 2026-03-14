## Software Design Principles

:::::{grid} 1 3 3 3
:gutter: 2

::::{grid-item-card} Write "orthogonal" code

**<i class="fa-solid fa-arrows-left-right"></i> Orthogonal**

^^^

Two components are **orthogonal** if changing one does not affect the other.

Separate computation from visualization: change plot colors without re-running a 3-day clustering.
::::

::::{grid-item-card} Don't Repeat Yourself

**<i class="fa-solid fa-copy"></i> DRY**

^^^

Every piece of logic defined in exactly one place.

Fix a bug in one place, not five. But remember: *duplication is cheaper than the wrong abstraction.*
::::

::::{grid-item-card}  Single Source of Truth

**<i class="fa-solid fa-database"></i> SSOT**

^^^

 Every piece of information (data, config) should have a single, unambiguous representation.

_Example_: Git sets the version, all mentions of the version refer to it (e.g. `pyproject.toml`, container tag, `docs/config.py`).
::::
:::::
