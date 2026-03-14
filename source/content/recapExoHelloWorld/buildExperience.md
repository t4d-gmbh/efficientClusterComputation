## Building & Running

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class: note

**Who managed to build and run the Apptainer container locally?**

- With or without help?
- Any issues encountered during setup?
:::

---

:::::{grid} 1 2 2 2
:gutter: 2

::::{grid-item-card} <i class="fas fa-check-circle"></i> Expected Workflow

1. 📤 **Configure**  

   ```apptainer
   %files
     pyproject.toml /app/
     ...
   %post
     ...
     uv pip install --no-cache --compile-bytecode .
     ...
   %environment
     ...
     export PATH="/opt/venv/bin:$PATH"
     ...
   %runscript
     ...
     exec "$@"
   ```
2. ⚙️ **Build**  
   
   Execute from the repository root, passing the manifest path:
   
   ```bash
   apptainer build img.sif containers/pipeline.def
   
   ```
3. 🚀 **Execute**  
   Apptainer supports the `--env-file` option for runtime injection:
   ```bash
   apptainer run --env-file .env img.sif
   ```
::::
::::{grid-item-card} <i class="fas fa-circle-question"></i> In your case:
:class: sd-m-auto

```{compound}
{.centered}
{.bigger}
What files did you include?  

What configuration step did you define?  

Did you need `%environment` at all?  

How can your container be run? `run`, `exec` or `shell`?  
```

::::
:::::
