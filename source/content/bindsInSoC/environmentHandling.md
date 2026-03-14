## Environment Management

{% if slide %}
**The Dual-Environment Architecture:**
* **Inner Environment:** The controlled, immutable runtime context inside the container.
* **Outer (Host) Environment:** The unpredictable runtime context where the container is executed.

*Note: The container build environment is a third context, but it must strictly not impact runtime reproducibility.*
{% endif %}

{% if page %}
The utilization of containers introduces a dual-environment architecture that must be carefully managed to ensure reproducibility:

* **Inner Environment:** The isolated runtime context of the project within the container. It can be fully declared and controlled during the build phase, ensuring immutable execution paths and fixed dependency locations.
* **Outer Environment:** The host runtime context where the container itself is executed. This constitutes the "unpredictable" environment, as it is determined by the specific hardware, operating system, and configurations of the execution node.

```{admonition} The 3rd Environment
:class: note
In principle, a third environment exists which could—but strictly should not—affect the reproducibility of a project: the **container build environment**. True reproducibility relies exclusively on the immutable built artifact and explicitly controlled runtime inputs, independent of the host where the image was originally compiled.

```

### Implications of Control
{% endif %}

{% if slide %}
::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item-card} Control

Implications

^^^

* **Fixed Paths:** High control over the inner environment enables the reliable use of fixed, default paths for configuration and data.

  ```python
  import os
  config_json = os.environ.get("CONFIG_PATH",
                               "./config/hello_to.json")
  ```
* **Dynamic Variables:** Sensitive or node-specific data (e.g., API tokens) cannot be hardcoded and **must be passed across the environment boundary at runtime**.
:::
:::{grid-item-card} Mechanisms in Apptainer

Injection

^^^
{% else %}

Because the inner environment is highly controlled, fixed or default internal paths can be reliably utilized for application resources. For example, default locations for configuration files can be safely established within the application logic:

```python
import os
# The application attempts to retrieve CONFIG_PATH, defaulting to an immutable internal path
config_json = os.environ.get("CONFIG_PATH", "./config/hello_to.json")

```

Conversely, dynamic runtime variables, such as access tokens, authentication keys, or specific remote API endpoints, cannot be embedded within the static container image. These values must be explicitly injected from the unpredictable outer environment into the inner environment at the moment of execution.

### Environment Injection

{% endif %}

{% if slide %}

* `--env-file`: Loads key-value pairs from a specified text file.
* `--env`: Sets variables directly via the command-line execution string.
* `APPTAINER_` Prefix: Host variables utilizing this prefix are automatically injected (with the prefix stripped).
:::
::::
{% else %}

Environment variable injection facilitates the secure transfer of dynamic or sensitive information from the host system into the container's isolated inner environment. This is achieved through several methods supported by Apptainer during `run`, `exec`, and `shell` commands:

* **The `--env-file` flag:** Specifies a file containing key-value pairs to be loaded as environment variables inside the container.
* **The `--env` flag:** Allows environment variables to be defined directly within the command-line execution string.
* **Host Prefixing:** Any environment variable defined on the host system prefixed with `APPTAINER_` is automatically injected into the container environment. The `APPTAINER_` prefix is stripped during the injection process (e.g., defining `APPTAINER_API_TOKEN` on the host makes `API_TOKEN` available inside the container).
{% endif %}
