# CS60075 Project 10: Open-Domain Question Answering

This package implements and evaluates all three required tasks on TriviaQA
`rc.nocontext`. Read `docs/SUBMISSION_CHECKLIST.md` before submitting.

## Lightweight GitHub copy

Every uploaded file is smaller than 250 KiB. The video, datasets, model weights,
archives, large prediction dumps, and font binaries are excluded. Code, the
notebook, presentation, and small measured text outputs are included. The PDF
report and chart image are generated locally instead of uploaded.
See `docs/GITHUB_CONTENTS.md` for the exact scope and reproduction limits.

## Quick start (Google Colab)

1. Open `Run_Experiment.ipynb` in Colab (File > Upload notebook).
2. Download this repository using GitHub **Code > Download ZIP**, then upload that small source ZIP in the first notebook cell.
3. Keep the default pinned CPU environment, or select the documented optional GPU wheel.
4. The default `RESTORE_SNAPSHOT=False` rebuilds Wikipedia and runs a new experiment.
   If you already have the separate reference data snapshot, set it to `True`
   and restore that snapshot in Colab; it is intentionally not hosted on GitHub.
5. The notebook runs preparation, Tasks 1/2/3, analysis, report generation, and
   downloads. Network access is needed for public datasets and pretrained models.

No API key, paid service, model training, or private data is required. The package
uses a small open generator to support CPU execution. Setup and live corpus
collection take time; a GPU is optional, not required. The reference experiment
was run on Linux/Python 3.12 with CPU PyTorch.

## Local setup

Use a **fresh Python 3.12 virtual environment**, particularly if you already have
NumPy 2 or newer NLP packages installed:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
export PYTHONHASHSEED=42
export TOKENIZERS_PARALLELISM=false
python -m pytest tests -q
python code/run.py all
```

Font binaries are excluded from GitHub. Report generation uses the Vera fonts
installed with ReportLab, or the original DejaVu fonts if supplied locally.
`report.pdf` is generated locally; the PDF is not uploaded to GitHub.

The Colab notebook satisfies the assignment's notebook-or-Docker requirement.
A Dockerfile is unnecessary and is not claimed to have been tested.

`requirements.txt` pins the observed reference environment, including the CPU
PyTorch build and the exact spaCy model wheel. GPU runs may use torch 2.6.0
from the CUDA 12.4 wheel index as shown in the notebook. A changed device causes
new run identities; GPU behavior has not been independently validated here.

## Separate stages

Run from the project root; the scripts also resolve paths independently of cwd:

```bash
python code/run.py prepare
python code/run.py task1
python code/run.py task2
python code/run.py task3
python code/run.py analyze
python code/run.py report
```

- `prepare`: sample 300 unique validation IDs with seed 42, build/freeze Wikipedia.
- `task1`: BM25 k1=1.5, b=0.75; top passage; highest-overlap sentence; longest
  noun phrase or numeric span. A classical NLTK averaged-perceptron POS tagger
  and a regular-expression noun-phrase grammar supply linguistic structure. Equal-length spans prefer character length, then position.
- `task2`: frozen MiniLM, normalized exact FAISS cosine search, top-5 RAG.
- `task3`: RRF of top-50 lexical/dense candidates, constant 60; same generator.
  This stage is independently runnable after preparation. To analyze both the
  original Task 2 answers and hybrid answers, run Task 2 before `analyze`.
- `analyze`: all task tables, plot, examples, Task-2/Task-3 overlap statistics,
  independent support audit template, and support accuracy when valid labels exist.
- `report`: generates an 8-12 page main report plus references, directly
  from saved results. It refuses to invent missing task results.

`all` runs these stages sequentially and shares loaded resources between tasks.
Individual stages can resume from per-question checkpoints. Matching run identity
requires the same data hashes, functional code, configuration, device, and torch
version. Model loading and vector construction are separate setup costs; saved
per-question timings include retrieval and answering only.

## Files and outputs

```text
code/
  task1/baseline.py            BM25 and classical span extraction
  task2/dense.py               MiniLM + IndexFlatIP
  task2/generator.py           frozen Qwen generation and exact token truncation
  task3/hybrid.py              reciprocal rank fusion
  task3/groundedness.py        lexical answer/evidence warning
  common/io.py                atomic files, checksums, deterministic settings
  common/metrics.py           aliases, EM/F1, retrieval proxy, overlap
  common/experiment.py        separately runnable task pipelines and checkpoints
  prepare.py                  question sampling and question-only Wikipedia search
  analyze.py                  measured comparison, audits, and visualization
  make_report.py              reproducible PDF report generation
  package_submission.py       ZIP packaging without large data/models
results/
  task1/, task2/, task3/       small metrics.json; predictions regenerated locally
  comparison.csv/.json/.md     all required task metrics
  comparison.png              generated locally by analyze; excluded from GitHub
  groundedness.json           overlap and support audit metrics
  qualitative_examples.json   deterministic outcome-stratified examples
  support_audit.json          fixed-seed evidence-support review set
  support_labels.csv          reviewed pilot labels, when provided
  support_labels_TEMPLATE.csv blank annotation template
  tests.txt                   actual unit-test output
report.pdf                    generated locally from measured summaries
Run_Experiment.ipynb           end-to-end Colab workflow
config.json                   fixed hyperparameters + immutable revision hashes
metadata.json                 author information (review group members)
docs/                         walkthrough and submission checklist
```

Preparation additionally writes `data/questions.jsonl`, `data/corpus.jsonl`,
`data/articles/`, `data/search_cache/`, and provenance manifests. Dense vectors go
in `cache/embeddings.npy`. Per-question checkpoint files go in
`results/task*/records/`; these redundant checkpoints are excluded from the ZIP.
This GitHub copy includes summary results and small manifests. Consolidated
per-question predictions, large data files, and pretrained checkpoints are
excluded; running the task stages regenerates predictions locally.

## Dataset and pretrained checkpoint sources

- Dataset: https://huggingface.co/datasets/mandarjoshi/trivia_qa
- Configuration: `rc.nocontext`; validation split only for scoring.
- Encoder: https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2
- Generator: https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct
- Corpus-query preprocessing only: https://github.com/explosion/spacy-models/releases/tag/en_core_web_sm-3.8.0
- Classical baseline POS tagger: https://raw.githubusercontent.com/nltk/nltk_data/gh-pages/packages/taggers/averaged_perceptron_tagger_eng.zip
  (SHA-256 pinned in code/task1/baseline.py; NLTK 3.9.1).
- Wikipedia search/pages: https://en.wikipedia.org/w/index.php and https://en.wikipedia.org/wiki/
- Assigned paper: https://arxiv.org/abs/2005.11401
- Reference DPR repository: https://github.com/facebookresearch/DPR

Immutable dataset and encoder/generator revisions are in `config.json`. The
installed spaCy wheel is pinned in `requirements.txt`. Public dataset/model
licenses and Wikipedia attribution remain applicable to redistributed content.
The Wikipedia article titles, URLs, revision IDs, and retrieval timestamps are
preserved in the source snapshot and result evidence.

## Corpus assumptions and leakage controls

Dataset 10.3 refers to Dataset 10.2's construction instructions. That section was
not attached. The implementation therefore documents this explicit assumption:
for each question, search Wikipedia using only question-derived terms, use the
first nonempty query and the first returned page, then index their global
union. No gold answer, alias, or original evidence-page metadata is used to pick
pages. Topic targeting is allowed by the supplied brief but limits generalization
claims. The TA should confirm this construction before final submission.

The dataset library may download/prepare train and test files when loading the
configuration. They are not consumed for model training or few-shot exemplars.
The evaluation sample is deduplicated by question ID before sampling. The
actual loaded split sizes can differ from the handout's approximate figures.

Public search HTML supplies uncached search results; earlier successful API search
results remain frozen in the search cache. Full article HTML is parsed for paragraph
text, avoiding the API bulk full-extract limit. A missing article body causes
a visible preparation error. Legitimately short or paragraph-free pages contribute
no passages and are listed explicitly in the corpus manifest. Real page/revision IDs are
parsed when exposed; byte checksums and the snapshot preserve exact text regardless.

Passages are paragraph-based, at most 120 words, minimum 20 words, no overlap,
with exact-text deduplication. All three methods reuse identical passages.
The generator sees at most 200 **generator tokens per passage**, max 64 new
tokens, and greedy decoding (temperature-zero equivalent). The identical prompt
serves dense and hybrid RAG. The 0.8 overlap threshold is fixed, not fitted on
final answer correctness. Task 3 flags low overlap; it does not change the main
answer scores by silently dropping questions.

## Evaluation definitions

EM and multiset-token F1 maximize over `answer.value` plus every answer alias.
Normalization lowercases, deletes ASCII punctuation, removes a/an/the, and
collapses whitespace. Empty answers are abstentions and score zero for these
nonempty reference answers.

There are no gold passage-relevance labels in the custom corpus. The reported
Recall@1/3/5/10 is explicitly an **answer-containing passage success proxy**:
at least one normalized gold alias occurs as a whole token sequence in a
retrieved passage. It is not semantic relevance recall or the fraction of all
relevant corpus passages retrieved. Gold aliases are used only after retrieval.
Post-hoc corpus answer coverage is reported separately and never used to remove
hard questions or add answer-targeted pages.

Support is not correctness. `support_labels.csv` labels whether the *supplied
evidence supports the proposed answer to the question*, independently of EM.
The fixed-seed pilot includes ten questions evaluated under both generators.
Support requires the answer relation and material question constraints, including
dates, to agree with the supplied evidence; a conflicting date is not ignored
merely because the entity name appears. These strict judgments are reviewable.
If assistant-reviewed labels are supplied, the annotator is explicitly disclosed;
these are not human-validated gold labels. Review them yourself before relying
on support-check accuracy. Accuracy is reported only on nonempty answers in the labeled audit, with
its effective size; abstentions are excluded from claim-support accuracy. Empty predictions are labeled unsupported by convention and also
tracked separately as abstentions. The labels record run identities and question/answer/evidence digests.
Only an identical reviewed claim can reuse a support judgment, even if an
unrelated source file changes the overall run fingerprint.

To annotate or replace the audit: read `support_audit.json`; fill the template's
`supported` column with 0/1, plus `annotator` and `rationale`, preserve `task`,
`question_id`, `run_identity`, and `claim_digest`, save as `support_labels.csv`, rerun analyze/report.
Do not use the answer aliases as support labels. A name can appear in a passage
without answering the question; negation and wrong entity roles are common
high-overlap false positives.

## Exact snapshot versus a fresh collection

`NLP_QA_Data_Snapshot.zip` is a separately retained companion and is not uploaded
to GitHub. If you have it, copy its `nlp_qa_project/data/` contents into this
repository's `data/` directory (or use the optional notebook restore cell).
It restores the exact reference question/corpus bytes.
Preparation checks the frozen corpus checksum and reuses it. Pretrained models
still download from their pinned sources; no weights are bundled.

A live Wikipedia rebuild is a new experiment, even if article IDs remain the
same. Revision IDs alone do not guarantee byte-identical text extraction across
API changes. Preserve the snapshot for exact corpus reproducibility. Do not
claim byte-identical results from a newly reconstructed live collection.

## Troubleshooting and safe resume

- If a Wikipedia request fails after bounded retries, preparation records the
  failure in `data/preparation_errors.json` and stops rather than silently
  weakening the corpus. Rerun `prepare`; completed requests stay cached.
- If dependencies conflict, create a new Python 3.12 environment and install the
  pinned requirements together. Do not mix with existing NLP environments.
- Model/dataset downloads need internet and sufficient free disk space.
- To change sample/corpus settings, use a fresh project folder. Frozen data
  cannot silently be reused for a different sample configuration.
- Changing evidence/model/code invalidates prediction checkpoints. Independently
  re-annotate support labels for the new run before calculating audit accuracy.
- `code/package_submission.py` rebuilds a ZIP without models, datasets, virtual
  environments, or embedding caches. Inspect the final archive before submission.

The walkthrough video is excluded from GitHub. Its transcript and chapter times
remain in docs/. The editable presentation includes notes for a 10-minute talk.
Review and rehearse the material before presenting it yourself. Keep any video
required for course submission separately from this repository.

Walkthrough narration uses Piper 1.3.0 and the public en_US-lessac-medium voice:
https://github.com/OHF-Voice/piper1-gpl and
https://huggingface.co/rhasspy/piper-voices/tree/main/en/en_US/lessac/medium .
