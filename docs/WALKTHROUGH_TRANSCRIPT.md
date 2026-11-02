# Narrated code walkthrough

Synthetic narration of the actual implementation and measured outputs.

## 1. Project structure

This is an automated walkthrough of the open-domain question answering experiment for CS six zero zero seven five, Project Ten. The narration is synthetic. The code and result files belong to the accompanying experiment package. The project compares three systems on the same three hundred unique Trivia Q A validation questions. Each system first retrieves evidence and then produces an answer. The first system uses keyword retrieval and rule-based extraction. The second uses dense retrieval and an instruction model. The third combines the retrieval rankings and adds an evidence overlap warning. The folders are separated by task so that each contribution can be inspected and run independently. The single entry point runs preparation, each task, analysis, and report generation.

## 2. Question sampling and Wikipedia

Preparation loads the assigned no-context configuration of Trivia Q A. That configuration has questions and accepted answers, but its evidence fields are empty. The code deduplicates validation examples by question identifier before sampling, so repeated rows cannot count as independent questions. A fixed random seed, forty two, selects three hundred unique questions. Canonical answers and answer aliases are saved for scoring. They are never passed to the retrievers, the language model prompt, or Wikipedia search. Question words, named entities, and noun phrases provide the search queries. The first returned Wikipedia article for each question contributes to one shared corpus. Full article paragraphs are split into passages of at most one hundred twenty words, with a minimum of twenty words.

## 3. Task 1: classical baseline

Task One uses B M twenty five with k one equal to one point five and b equal to zero point seven five. This is a lexical retriever: it rewards overlap between question words and passage words. The system takes the highest-ranked passage, then chooses its sentence with the largest count of shared question words. Rule-based sentence splitting, a classical averaged-perceptron part-of-speech tagger, and a regular-expression grammar supply noun phrases. These are classical statistical and rule-based components. The longest noun phrase or numeric span becomes the answer. This deliberately weak rule can select the title of a book instead of its author, even when retrieval found a useful passage. The saved records include the selected sentence so that this failure is easy to inspect.

## 4. Task 2: dense retrieval

Task Two replaces lexical retrieval with the required Mini L M encoder. Each passage and question is encoded into a dense vector. The vectors are normalized to unit length. The F A I S S flat inner-product index then ranks them by cosine similarity, because inner product equals cosine for normalized vectors. Flat search is exact over the encoded vectors. It does not fix errors in the encoder or recover text truncated by that encoder. Passage vectors are cached together with the model revision and corpus fingerprint. A changed corpus cannot silently reuse an incompatible embedding cache. The system saves fifty candidate ranks so that all required retrieval cutoffs, including recall at ten, can be evaluated.

## 5. Task 2: evidence-conditioned generation

The answer generator is the frozen open Qwen two point five, zero point five billion parameter instruction model. It receives the question and the top five retrieved passages. Each passage is truncated separately to two hundred generator tokens. This is not a two hundred word limit, and it is not a limit on the combined context. The prompt requests the shortest answer phrase supported by the evidence and permits unknown if evidence is insufficient. Greedy decoding implements the requested temperature-zero behavior, with one beam and at most sixty four new tokens. Unknown becomes an empty prediction. Other outputs are kept as generated; the accepted answer list is not used to repair them. Hybrid retrieval uses exactly the same model and prompt.

## 6. Task 3: reciprocal rank fusion

Task Three combines the lexical and dense candidate lists using reciprocal rank fusion. For each passage, add one divided by sixty plus its rank in each list where it appears. Ranks start at one. A missing-list contribution is zero. This combines ranking positions rather than raw scores, since B M twenty five scores and cosine scores have different scales. Candidates supported by both retrievers can move upward. The fused top five passages feed the same generator used in Task Two. The comparison between dense and hybrid R A G therefore holds the answering stage fixed. A retrieval improvement can still fail to improve the answer: the generator may misread the evidence or choose an incorrect entity.

## 7. Evaluation and evidence support

Answer evaluation maximizes exact match and token F one over every accepted alias. Normalization lowercases text, removes punctuation and English articles, and collapses whitespace. Retrieval recall is reported at one, three, five, and ten. Because this custom corpus has no passage relevance labels, it is explicitly an answer-containing passage success proxy. A passage can mention a gold answer without supporting it. The support checker is separate. It measures how many answer tokens appear in the actual truncated prompt evidence. Coverage below the fixed threshold of zero point eight triggers a warning. High overlap is not proof of support: the words can occur with the wrong relation, another subject, or a negation.

## 8. Running and resuming the experiment

The simplest entry point is the supplied Google Colab notebook. Upload the submission archive, install the pinned requirements, and run the preparation and task cells in order. To reproduce the exact reference corpus, restore the separate data snapshot. Rebuilding Wikipedia creates a new collection because public pages can change. For a local run, use a fresh Python three point twelve environment and the commands shown here. Each task can run independently after preparation. Per-question checkpoints allow an interrupted run to resume. The checkpoint identity includes the data, configuration, functional source, device, and Torch version. Analysis regenerates the comparison tables and plot from saved predictions. The report command then rebuilds the PDF from those measured files.

## 9. Measured results and paired outcomes

The completed experiment contains three hundred predictions for every task. The final classical baseline has zero percent exact match and five point zero three percent token F one. Dense R A G reaches nineteen point three three percent exact match and twenty six point four zero percent F one. Hybrid R A G reaches nineteen point six seven percent exact match and twenty eight point six one percent F one. Fusion corrects thirteen dense errors but loses twelve dense-correct answers, giving a net gain of one question. Answer-containing recall at five rises from forty five point three three to forty six point three three percent. These are modest measured differences on this fixed sample. The original R A G paper uses different test data, a different corpus, architecture, and training, so its published scores provide context rather than a controlled leaderboard comparison.

## 10. Interpretation and reproducibility

The corpus contains an accepted answer string for seventy nine percent of questions, so evidence gaps constrain the experiment. The overlap checker also misses relationships: it can accept a neighboring country instead of the country containing a province. A fixed pilot reviews ten questions under both generation methods. Its support labels were produced by the assistant and require human review. Excluding empty answers, support-check accuracy is fifty percent on eight dense answers and fifty five point five six percent on nine hybrid answers. These small denominators are stated explicitly. The final files include the PDF report, an editable presentation, commented Python modules, fourteen passing checks, and the Colab notebook. Restore the separate frozen snapshot to reproduce the same corpus. Review your group details and the documented corpus-construction assumption before using the materials for the assignment.
