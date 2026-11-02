# Lightweight GitHub contents

This repository intentionally contains only files smaller than 250 KiB each.
It includes all Python task modules, configuration, pinned requirements,
the Colab notebook, tests, provenance manifests, summary metrics, comparison
tables, qualitative examples, presentation, and walkthrough text.

Excluded from GitHub:

- The walkthrough video and all other video files.
- Submission/data ZIP archives, datasets, downloaded models, and embeddings.
- The three large `results/task*/predictions.jsonl` files and record caches.
- Font binaries; report generation uses ReportLab's installed Vera fonts.
- The report PDF and comparison PNG; generate them locally with the report
  and analyze stages, respectively. The small comparison tables remain included.

The included results describe the saved reference experiment. The
full per-question predictions and frozen corpus are not hosted in this repository.
To regenerate predictions, install the requirements and run preparation and
Tasks 1–3 before analysis. A fresh Wikipedia collection is a new experiment;
it cannot guarantee the same reference scores.

For the exact reference corpus, restore your separately retained data snapshot
outside GitHub. The small manifests record its question IDs, provenance, and
checksums. Keep any video or full submission archive separately if required
by the course. The written walkthrough and presentation remain available here.

`.gitignore` excludes the known large generated artifacts and video formats.
Before future uploads, check file sizes as Git ignore rules do not enforce a
general byte limit.
