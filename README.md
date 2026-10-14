# paper-garden

Physical AI papers from arXiv, collected twice a day and grown into a related-paper graph.

**Explorer:** https://hobeom.github.io/paper-garden/ — graph view, 2D/3D similarity map, list, stats and paper pages.

## Topics

| Topic | Papers |
|---|---|
| Physical AI | [arxiv/physical-ai](arxiv/physical-ai/) |
| Vision Language Action Model | [arxiv/vla-model](arxiv/vla-model/) |
| Robot & Agent | [arxiv/robot-agent](arxiv/robot-agent/) |
| World Models | [arxiv/world-models](arxiv/world-models/) |
| Robot Manipulation | [arxiv/robot-manipulation](arxiv/robot-manipulation/) |
| Humanoid & Locomotion | [arxiv/humanoid-locomotion](arxiv/humanoid-locomotion/) |
| Sim-to-Real | [arxiv/sim-to-real](arxiv/sim-to-real/) |
| Embodied Navigation & Driving | [arxiv/embodied-navigation](arxiv/embodied-navigation/) |

Topics, search phrases and arXiv categories live in [`config.yaml`](config.yaml).

## How it works

One workflow, [`.github/workflows/update.yml`](.github/workflows/update.yml), runs three tasks and commits the results:

| Task | Schedule (UTC) | What it does |
|---|---|---|
| `daily` | 01:41, 13:41 | `daily_arxiv.py` fetches new papers per topic, `enrich.py` adds Hugging Face info and Semantic Scholar references/citations/recommendations to `arxiv/graph.json`, `build_explorer.py` writes `docs/data.json` |
| `weekly-links` | Mon 08:00 | refreshes code links on existing papers and rebuilds the explorer data |
| `hf-prefetch` | Mon–Fri 09:00 | caches Hugging Face daily papers into `hf_cache/` |

Any task can also be started by hand from the Actions tab.

### Files

- `daily_arxiv.py` — arXiv fetch, per-topic `README.md` / `data.json`, abstracts in `arxiv/meta.json`
- `enrich.py` — Hugging Face paper info (`hf_cache/hf_papers.json`), Semantic Scholar graph (`arxiv/graph.json`) and bounded graph expansion (settings under `enrich` in `config.yaml`)
- `build_explorer.py` — TF-IDF + UMAP layout, sub-topic clusters, similar papers, optional Gemini summaries → `docs/data.json`
- `prefetch_hf_papers.py` — Hugging Face daily papers cache
- `docs/index.html` — the explorer page (static, served by GitHub Pages)

## Setup

1. **Secrets** (Settings → Secrets and variables → Actions)
   - `S2_API_KEY` — Semantic Scholar API key; keyless runs are heavily throttled
   - `GEMINI_API_KEY` — optional, only when `summary_per_run` in `config.yaml` is above 0
2. **Actions permissions** (Settings → Actions → General → Workflow permissions): Read and write
3. **Pages** (Settings → Pages): Deploy from a branch, `main`, `/docs`

### Run locally

```bash
python -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt
python daily_arxiv.py
S2_API_KEY=... python enrich.py
python build_explorer.py --summaries 0
python -m http.server -d docs   # http://localhost:8000
```

## License

[Apache License 2.0](LICENSE)
