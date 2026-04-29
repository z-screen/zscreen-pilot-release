# Z-Screen Pilot Release

Source for [www.z-screen.com](https://www.z-screen.com) and the five-preprint pilot release from Zafrens (April 2026).

> **Status: pre-bioRxiv draft.** These five manuscripts are draft preprints posted publicly so we can collect technical feedback before formal bioRxiv submission. The website is intentionally unindexed (`noindex` meta tag) during this period. Treat this as an early-look artifact, not a final release.

## What lives here

- `docs/` - the public website (served by GitHub Pages at www.z-screen.com)
- `docs/pdfs/` - the five preprint PDFs, plus the bundled all-in-one PDF
- `docs/CNAME` - custom-domain configuration

## What lives on Zenodo

The full reproducibility bundle (canonical dataset, per-paper analysis scripts, manuscripts) is deposited on Zenodo with a citable DOI:

**DOI: [10.5281/zenodo.19872807](https://doi.org/10.5281/zenodo.19872807)**

The bundle contains:
- 615,793 mRNA-seq profiles across 12 combinatorial libraries and 4 cell lines
- scVI latent coordinates, chemistry embeddings (raw SMILES replaced with embeddings, see audit script)
- Per-paper analysis scripts and reproduction READMEs (`paperN/scripts/`, `paperN/README.md`)
- Manuscripts in markdown and rendered PDF
- LINCS L1000 cross-platform benchmarking module

To re-run any paper's analysis end-to-end, download the Zenodo bundle and follow the per-paper README.

## The five preprints

| # | Title | Question |
|---|---|---|
| 01 | ActiveSeq | Can we measure what a compound does and read why, from the same well? |
| 02 | Generative Chemistry | Does combinatorial chemistry yield a learnable design map? |
| 03 | Generalization Ladder | Does the model generalize to new chemistry, or memorize the training set? |
| 04 | Cross-Cell Transfer | Does the response transfer across cell types we never assayed? |
| 05 | Causal Reasoning | Does the screen point at mechanism, automatically? |

Read the layman summaries at [www.z-screen.com](https://www.z-screen.com).

## Citation

If you use the dataset or build on this work during the draft period, please cite the Zenodo DOI (above). When the formal preprints post to bioRxiv, those will be the canonical citation.

## License

- Website code (HTML/CSS/JS) in `docs/`: MIT (see `LICENSE`)
- Preprint PDFs in `docs/pdfs/`: CC-BY 4.0 (draft versions)
- Dataset: see Zenodo deposit for license

## Contact

[z-screen@zafrens.com](mailto:z-screen@zafrens.com) - feedback, partnerships, collaborations

[www.zafrens.com](https://www.zafrens.com) - about Zafrens
