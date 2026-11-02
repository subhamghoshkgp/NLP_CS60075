# Submission checklist

Deadline shown in the supplied document: **8 November 2026**.

## Included

- Python source arranged under code/task1, code/task2, code/task3.
- Fixed-seed experiment configuration with dataset/model revision pins.
- Pinned requirements and an end-to-end Google Colab notebook.
- Report-generation code for the PDF with methods, scores, qualitative cases,
  original-paper comparison, and disclosed assumptions. Generate the PDF locally;
  it is excluded from GitHub.
- Small saved metrics, comparison tables, and selected qualitative evidence.
  The chart image regenerates locally during analysis.
  Full prediction dumps are excluded from GitHub and regenerate when tasks run.
- Walkthrough script and presentation material.
- No large datasets, model checkpoints, virtual environment, or embeddings in the submission archive.

## Student review before submission

1. Confirm that TriviaQA rc.nocontext is your group's assigned dataset.
2. Check Dataset 10.2 with the TA. It was referenced but not included in the supplied material;
   the question-only Wikipedia search procedure is an explicit assumption.
3. Add all group members and actual contributions to metadata.json and the report.
4. Review the assistant's pilot support annotations independently. These are not
   human-validated ground truth. Keep the audit denominator visible.
5. Open Run_Experiment.ipynb in Colab and execute the notebook, or reproduce locally.
6. Review the walkthrough transcript; use docs/WALKTHROUGH.md for your own recording if required. The video is intentionally excluded from GitHub.
7. Rehearse the 10-minute presentation. Be prepared to explain the code and failures.

Any video required by the course must be submitted separately from GitHub.
Review and rehearse the code so you can answer the TA's questions yourself.
See GITHUB_CONTENTS.md for the lightweight repository exclusions.
