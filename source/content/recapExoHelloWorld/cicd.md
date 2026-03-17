---
sd_hide_title: true
---
## CI/CD Build

:::{admonition} <i class="fa-solid fa-comments"></i> Discussion
:class: note margin

**Who managed get to publish a `.sif` file (Package)?**

- With or without help?
- What were the challenges?
:::
::::{grid} 1 2 2 2
:gutter: 2

:::{grid-item}

{% raw %}
```yaml
name: Build Apptainer Container
on:
  push:
    branches: [main]
    tags: ['*']

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository_owner }}/env-sif

jobs:
  build-apptainer:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Extract SCM versions
        id: scm
        run: |
          # The raw version hatch gives us (e.g., 0.1.dev51+gf41b6)
          RAW_VERSION=$(pipx run hatch version)
          
          # Version for Docker Tags (Replace + with -)
          CLEAN_VERSION=$(echo $RAW_VERSION | tr '+' '-')
          echo "tag_version=$CLEAN_VERSION" >> $GITHUB_OUTPUT

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            # The conditional directive is removed to force application
            type=raw,value=latest
            type=sha,format=short
            # Use the SANITIZED version for the registry tag
            type=raw,value=${{ steps.scm.outputs.tag_version }}
```
{% endraw %}
:::
:::{grid-item}
{% raw %}
```yaml

      - name: Setup Apptainer
        uses: eWaterCycle/setup-apptainer@v2
        with:
          apptainer-version: 1.3.1

      - name: Build Apptainer image
        run: |
          sudo apptainer build env.sif containers/env.def

      - name: Push Apptainer image to GHCR
        run: |
          echo "${{ secrets.GITHUB_TOKEN }}" | apptainer registry login -u ${{ github.actor }} --password-stdin oras://${{ env.REGISTRY }}
          TAGS="${{ steps.meta.outputs.tags }}"
          for TAG in $TAGS; do
            echo "Pushing to oras://$TAG"
            apptainer push env.sif "oras://$TAG"
          done
```
{% endraw %}
:::
::::
