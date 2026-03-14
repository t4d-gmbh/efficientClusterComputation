## Python Gotcha: Hidden Parallelism

```{admonition} See the handout
:class: seealso margin

For a deeper dive, check the [Research Software Engineering](https://t4d-gmbh.github.io/researchSoftwareEngineering/) course material on Python internals.
```

::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}
:columns: 6

```python
import numpy as np

size = 4000
arr1 = np.random.rand(size, size)
arr2 = np.random.rand(size, size)

for _ in range(100):
    np.dot(arr1, arr2)
```

This **already uses all your CPUs** — NumPy delegates to compiled C/Fortran BLAS libraries that run multi-threaded under the hood.
:::

:::{grid-item}
:columns: 6

:::{admonition} ⚠️ Oversubscription
:class: warning

Wrapping this in `multiprocessing` or parallel loops causes **thread contention**: each worker spawns its own BLAS threads across all cores, grinding everything to a halt.
:::

The same applies to `pandas`, `scipy`, `scikit-learn`, `pytorch`, and `tensorflow` — Python is the orchestration facade, the real work happens in compiled code.
:::
::::
