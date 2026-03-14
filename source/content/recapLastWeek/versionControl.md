## Version Control

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} Track your project content

Use it

^^^
Track code, configs, documentation, manuscripts.

**Ignore** environment specific files (`.env`, `*.sif`) > Use `.gitignore`.

Develop with small, frequent commits.
::::

::::{grid-item-card} `git tag -a '1.2.3' -m 'submitted to PLOS'` 

Write tags

^^^

Mark **meaningful milestones and states** with Git tags.

Maintain a `CHANGELOG` documenting version history.
::::

::::{grid-item-card} 

Use remote services

^^^

Mirror to **GitHub/GitLab/Forgejo** for backup and collaboration.

Leverage **Issues** for task tracking and **CI/CD** for automated testing and deployment.
::::

::::{grid-item-card} Version control your data

Use it for data

^^^

Use **Git LFS** or **DVC** to version-control datasets alongside your code.

Raw data and large files don't belong in plain Git.
::::
:::::
