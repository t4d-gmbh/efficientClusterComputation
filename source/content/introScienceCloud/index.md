# <i class="fa-solid fa-cloud"></i> The UZH Science Cloud

```{admonition} OpenStack at UZH
:class: tip, margin

Operated by [S3IT](https://docs.s3it.uzh.ch/cloud2/overview/).
```

{% if slide %}
<!-- BUILDING THE SLIDES -->
```{toctree}
:maxdepth: 1

./specsUZHCloud
./accessGUI
./accessCLI
```

{% else %}
<!-- BUILDING THE PAGES -->
```{include} ./specsUZHCloud.md
```
```{include} ./accessGUI.md
```
```{include} ./accessCLI.md
```
{% endif %}
