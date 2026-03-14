## SoC: Drain Your Scripts!

```{admonition} Separation of Concerns
:class: tip margin

Logic, parameters, environment, and data must never be tangled together.
```

:::::{grid} 2
:gutter: 2

::::{grid-item-card} <i class="fa-solid fa-layer-group"></i> The 4 Pillars
- **Environment**: `.env`, containers, `pyproject.toml`
- **Configuration**: `config/` (YAML/TOML params)
- **Code**: `src/` (reusable, stateless logic)
- **Data**: `data/` (raw → interim → final)
::::

::::{grid-item-card} <i class="fa-solid fa-wand-magic-sparkles"></i> The Conductor
Scripts in `scripts/` are minimal orchestrators that **combine** all four pillars at runtime.

No hardcoded paths. No embedded parameters. No mixed concerns.
::::
:::::

---

:::::{grid} 2
:gutter: 2

::::{grid-item-card} ❌ Tangled
```python
df = pd.read_csv("/home/me/data.csv")
result = process(df, threshold=0.5)
result.to_csv("/home/me/out.csv")
```
::::

::::{grid-item-card} ✔ Drained
```python
load_dotenv()
data_path = os.getenv("DATA_PATH")
cfg = yaml.safe_load(open("config/params.yml"))
df = pd.read_csv(data_path)
result = process(df, **cfg)
```
::::
:::::
