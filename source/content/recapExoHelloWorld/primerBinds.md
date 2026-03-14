### A primer on binds

::::::{grid} 1 1 1 1
:gutter: 2

:::::{grid-item-card} <i class="fas fa-plug"></i> Automated Binds

::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item-card} <i class="fas fa-charging-station"></i> Build Binds

"Connect" the container to the actual kernel (and thus to the hardware):
- `/proc`
- `/sys`
- `/dev` (partially)

Bind to the OS infrastructure
- `/tmp` \& `/var/tmp`

:::{admonition} Clear `APPTAINER_BINDPATH` during build-time
:class: warning
If not unset, any location will be bound on build-time and might overwrite essential container content.

Examples:

During `%post` the compiler might link against an `.so` file from `/apps`.

`npm` might import user config from `$HOME` > `/home/user` altering the installation behaviour.
:::

:::
:::{grid-item-card} <i class="fas fa-play"></i> Runtime Binds

_Extends the binds present at build time already._

Bind to the OS infrastructure (UID and GID mappings)
- `/etc/*` (some sub-directories) 
- `$HOME`
- `$PWD`

"Convenience" binds (defined in `APPTAINER_BINDPATH`), e.g.:

- `/home` (useless - unless access outside `$HOME` is needed)
- `/scratch`
- `/shares`
- `/apps`
:::
::::
:::::
::::::
