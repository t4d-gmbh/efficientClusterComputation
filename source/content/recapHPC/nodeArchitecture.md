{% if build == "slides" %}
# Inside & Between Nodes
{% else %}
## Intra- vs. Inter-Node Communication
{% endif %}

```{figure} ./../_static/clusterNodeNetwork.png
:alt: Intra- and Inter-Node Architecture
:width: 100%
```

{% if slide %}
:::{admonition} Key takeaway
:class: warning
Communication *within* a node is fast (shared memory, NVLink).
Communication *between* nodes goes through the network — orders of magnitude slower.
:::
{% endif %}

{% if page %}

Within a node, CPUs and GPUs share local memory and communicate over fast internal buses (PCIe, NVLink).
Between nodes, data travels over the network interconnect (typically InfiniBand), which is significantly slower.
This boundary directly affects how parallel workloads should be designed.

{% endif %}
