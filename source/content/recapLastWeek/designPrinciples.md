## Software Design Principles

:::::{grid} 3
:gutter: 2

::::{grid-item-card} <i class="fa-solid fa-arrows-left-right"></i> Orthogonal
Two components are **orthogonal** if changing one does not affect the other.

Separate computation from visualization — change plot colors without re-running a 3-day clustering.
::::

::::{grid-item-card} <i class="fa-solid fa-copy"></i> DRY
**Don't Repeat Yourself.** Every piece of knowledge should have a single, unambiguous representation.

Fix a bug in one place, not five. But remember: *duplication is cheaper than the wrong abstraction.*
::::

::::{grid-item-card} <i class="fa-solid fa-database"></i> SSOT
**Single Source of Truth.** Every data element defined in exactly one place.

Example: the version number lives in `pyproject.toml` — everything else reads it from there.
::::
:::::
