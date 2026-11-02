# Code walkthrough: approximately 8 minutes

Use your actual terminal/notebook. Show the saved real outputs before launching
a slow full rerun. This recording guide accompanies the computer-narrated video and editable transcript.

## 0:00-0:45 - Problem and package

Explain retrieval and answering as separate stages. Open the three task folders.
State that the question sample and Wikipedia collection stay fixed for all tasks.

## 0:45-1:45 - Data preparation

Open config.json and data/sample_manifest.json. Show the seed, question IDs,
immutable dataset/model revisions, and alias preprocessing in common/metrics.py.
Open prepare.py: Wikipedia queries use only question text; answer strings do not
affect corpus construction. Show article revisions and the corpus checksum.
Explain the missing Dataset 10.2 assumption and the frozen companion snapshot.

## 1:45-2:45 - Task 1

Open task1/baseline.py. Explain BM25 k1=1.5, b=0.75. Show the top passage,
highest-overlap sentence, and longest noun-phrase/number selection. Open one
task1 record and point out why the selected noun phrase can be wrong.

## 2:45-4:00 - Task 2

Open task2/dense.py: normalized embeddings, IndexFlatIP, cosine equivalence.
Open generator.py: same frozen model, exact 200-token truncation per passage,
top 5, greedy decoding, max_new_tokens=64. Show one successful output and one
failure. Explain that frozen MiniLM + Qwen is the course adaptation of the RAG
idea, not the original DPR/BART trained model.

## 4:00-5:00 - Task 3

Open task3/hybrid.py. Compute one example RRF contribution 1/(60+rank).
Explain why adding raw BM25 and cosine scores would be inappropriate.
Open groundedness.py and metrics.overlap_score. Show a case where an incorrect
answer is mentioned in evidence and therefore receives a misleading high score.

## 5:00-6:15 - Evaluation

Open results/comparison.csv and comparison.png. Explain EM, alias-aware F1,
and Recall@1/3/5/10 as answer-containing passage success proxies.
Open groundedness.json. Distinguish answer correctness from evidence support,
and explain pilot audit size, annotator, and the limitations of these labels.

## 6:15-7:15 - Reproducibility

Show the Colab notebook setup and separate task cells. Explain checkpoints and
run identities. Run python -m pytest tests -q. Show how analyze and report
regenerate tables and report.pdf from existing predictions.

## 7:15-8:00 - Conclusions

State the actual best system and measured hybrid-versus-dense difference.
Discuss corpus gaps, the weak longest-phrase baseline, model size, and lexical
verification failures. Explain why your result is not directly comparable to
the original paper's full test benchmark. End by identifying the output files.
