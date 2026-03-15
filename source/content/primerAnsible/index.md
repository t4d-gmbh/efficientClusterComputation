# Ansible Detour


```{admonition} jnfrastructure as Code (IaC)
:class: tip, margin


{% if slide %}
**Core Concept:**
* The management and provisioning of computing infrastructure through machine-readable definition files.
* Replaces physical hardware configuration and interactive graphical interfaces (GUIs).

**Architectural Advantages:**
* **Strict Reproducibility:** Infrastructural states are codified into version-controlled text files.
* **Auditability:** Infrastructure modifications are tracked via standard Git workflows.
* **Consistency:** Eliminates the risk of manual configuration drift across disparate deployment environments.
{% else  %}

Infrastructure as Code (IaC) is defined as the management and provisioning of computing infrastructure through machine-readable definition files, rather than through physical hardware configuration or interactive graphical interfaces.
By codifying infrastructural states into version-controlled text files, deployment environments are rendered strictly reproducible and auditable, thereby eliminating the risk of manual configuration drift.

{% endif %}
```

{% if slide %}
<!-- BUILDING THE SLIDES -->
```{toctree}
:maxdepth: 1

./ansible101.md
./oneShotConfig.md
```

{% else %}
<!-- BUILDING THE PAGES -->
```{include} ./ansible101.md
```
```{include} ./oneShotConfig.md
```
{% endif %}
