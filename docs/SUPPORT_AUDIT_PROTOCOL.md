# Evidence-support audit

Ten question IDs were selected with seed 43 from the fixed evaluation order,
then reviewed under both retrieval methods: 20 question/answer/evidence cases.
The question, generated answer, and all five exact prompt passages were read.
The judgments are by the OpenAI assistant, not human-validated ground truth.

A positive label requires support for the proposed answer's relation to the
question, including material entity roles and temporal constraints. Mere word
presence is insufficient. An empty answer is stored as zero for bookkeeping,
but is excluded from claim-support accuracy because it makes no proposed claim.
The reported accuracy denominator is the nonempty reviewed subset, separately
for dense and hybrid RAG. All 300 questions still count toward EM/F1 and the
requested correct/high-overlap and incorrect/low-overlap rates.

One judgment deserves particular human review: dense RAG answers Aidan to a
question asking about a 635 foundation. The evidence identifies Aidan as founder
but explicitly says before the end of 634. Under the strict full-question
criterion this is labeled unsupported, despite supporting the founder identity.
An identity-only protocol would label it differently. This makes the annotation
assumption visible rather than treating a small pilot as objective gold truth.

Other reviewed negative cases confuse a neighboring country with the country
containing a province, a childhood/legal name with a stage name, or an unrelated
civilization with the civilization containing a city. Each label has its own
rationale in results/support_labels.csv. Claim digests prevent changed outputs
or evidence from silently inheriting these labels.
