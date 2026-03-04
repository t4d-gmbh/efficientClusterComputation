# Containers for HPC


{% if slide %}
<!-- BUILDING THE SLIDES -->
```{toctree}
:maxdepth: 1

./OSlevelVirtualization.md
./dockerVS
./apptainerVS
./hpcContext
./systemPackages
./invisibleContainer
./iCDefinition
./clusterExo1
```

{% else %}
<!-- BUILDING THE PAGES -->

```{include} ./OSlevelVirtualization.md
```
```{include} ./dockerVS.md
```
```{include} ./apptainerVS.md
```
```{include} ./hpcContext.md
```
```{include} ./systemPackages.md
```
```{include} ./invisibleContainer.md
```
```{include} ./iCDefinition.md
```

{% endif %}

