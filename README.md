# Astrobiology Analogue Sites — Code

Code accompanying the MSc dissertation *Systematic Identification of (Pre-)Noachian, Hesperian,
and Amazonian Terrestrial Mars Analogues*. This finds candidate Mars-analogue sites on Earth by
searching the literature, screening it with an LLM against a geological "epoch matrix," and
mapping the results.

The supporting PDFs and spreadsheets (including the final catalogue,
`Analogue_Catalogue_Finalised_WITH_APPENDIX.xlsx`) are included alongside this code rather than
in this repo listing.

## Pipeline order

Run the notebooks in this order. Each one reads the previous notebook's output.

| # | Notebook | What it does | Key outputs |
|---|----------|---------------|--------------|
| 1 | `01_literature_search_ads.ipynb` | Searches NASA ADS by epoch, builds a citation network around seed papers | `triage_ready.csv` |
| 2 | `02_literature_search_semantic_scholar.ipynb` | Same search on Semantic Scholar, merges with the ADS results | `combined_triage_ready.csv` |
| 3 | `03_ai_triage_pipeline.ipynb` | Screens every paper with an LLM against the epoch matrix, filters to high-confidence/novel sites, clusters nearby sites, extracts detailed parameters from full-text PDFs | `martian_analogue_detailed_master.csv`, PDF download manifests, `final_thesis_catalogue.csv` |
| 4 | `04_analogue_site_maps.ipynb` | 8 global + regional site maps | `01_global_overview_epoch.png` … `08_antarctica_locator.png` |
| 5 | `05_terrain_site_maps.ipynb` | Zoomed DEM-terrain maps per site cluster | terrain PNGs |
| 6 | `06_dissertation_figures.ipynb` | All 11 dissertation figures | `fig1_…png` … `fig11_…png` |

Notebooks 4–6 all read the same finished catalogue
(`Analogue_Catalogue_Finalised_WITH_APPENDIX.xlsx`) rather than each other's output, so they can
be run in any order once that file exists — the numbering above just reflects the pipeline's
overall logic (literature → AI triage → maps/figures).

`archive/` holds earlier drafts of the maps/figures notebooks, kept for reference; the
numbered notebooks above are the final versions.

## Setup

```bash
pip install requests pandas numpy scipy matplotlib openpyxl pydantic
pip install google-genai anthropic geopy          # notebook 3 (AI triage)
pip install adjustText contextily                 # notebooks 3–6 (maps, optional)
conda install -c conda-forge rasterio elevation    # notebook 5 (DEM terrain, optional)
conda install -c conda-forge geopandas geodatasets # notebook 4 (real coastlines, optional)
```

Optional packages (`adjustText`, `contextily`, `rasterio`/`elevation`,
`geopandas`/`geodatasets`)  — if missing, the notebooks fall back to simpler labels/basemaps/outlines rather than failing.

## API keys

Notebook 1 needs a free [NASA ADS API key](https://ui.adsabs.harvard.edu/user/settings/token).
Notebook 3 needs a [Gemini](https://aistudio.google.com/apikey) and/or
[Anthropic](https://console.anthropic.com/) API key.

Set these as environment variables rather than editing the notebooks:

```bash
export ADS_API_KEY="your-key-here"
export GEMINI_API_KEY="your-key-here"
export ANTHROPIC_API_KEY="your-key-here"
```

> **A previous version of this code had live API keys hardcoded directly in the notebooks.**
> Those keys have been removed from every file here, but if you're re-publishing this project
> from an older copy, **revoke and regenerate any key that was ever committed** — removing it
> from the file afterwards doesn't undo the exposure.

Semantic Scholar (notebook 2) works without a key on a slower rate limit; a free key raises the
limit significantly.

## Notes

- Notebook 1's citation-network step and notebook 2's `seed_ids` need seed-paper
  bibcodes / Semantic Scholar paper IDs filled in before running.
- Notebook 3's PDF steps expect manually downloaded PDFs, named by `bibcode`, in a
  `shortlisted_pdfs/` folder.
- All outputs are written to the working directory the notebook is run from.
- Shortlisted PDFs are not listed due to copyright, all PDFs can be found using their bibcode given in the catalogue. 
