## Version Control

```{admonition} Non-negotiable
:class: tip margin

If it's human-created, it belongs in version control.
```

:::::{grid} 2
:gutter: 2

::::{grid-item-card} 1. Use it
Track **all** human-created files — code, configs, documentation, manuscripts.

Small, frequent commits > large, rare ones.
::::

::::{grid-item-card} 2. Write tags
Mark meaningful milestones with **Git tags**.

Maintain a `CHANGELOG` documenting version history.
::::

::::{grid-item-card} 3. Use remote services
Mirror to **GitHub/GitLab** for backup and collaboration.

Leverage **Issues** for task tracking and **CI/CD** for automated testing and deployment.
::::

::::{grid-item-card} 4. Use it for data too
Raw data and large files don't belong in plain Git.

Use **Git LFS** or **DVC** to version-control datasets alongside your code.
::::
:::::
