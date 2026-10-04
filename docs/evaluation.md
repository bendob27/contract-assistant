# Evaluation

No result is assumed in advance. Fine-tuning may not beat retrieval with the base model, and an external model may not beat the private ones on complex contracts. The evaluation exists to find out, and it decides what goes into service. Its measured results are not published in this repository.

## Questions it answers

| # | Question | What it decides |
| --- | --- | --- |
| 1 | Is the extracted text reliable enough to build on? | Whether extraction needs work before anything else |
| 2 | How good is the untuned base model with retrieval and the current playbook? | The baseline that every later result is compared with |
| 3 | Does the tuned specialist beat that baseline on unseen contracts? | Whether an adapter is deployed at all |
| 4 | On cases whose data may leave, does an external model do better than the private ones? | Whether an external route is opened for that kind of task |
| 5 | Do permissions and routing hold under test? | Whether the assistant may be used in Slack |
| 6 | Do the base model and the adapter run together on one GPU at a usable speed? | Whether one GPU is enough |

## Test data

- The split into training, validation and test sets is made before any examples are generated.
- Related contracts and near-duplicates stay in the same set, so the test set holds only contracts the model has not seen in any form.
- The tuned and the untuned model are compared on the same unseen contracts.

## What is checked

| Area | Checks |
| --- | --- |
| Extraction | Numbers, negations, tables and scanned pages, compared with the source document |
| Retrieval | The right contract and clause are found. The source reference points to the right clause, and for PDFs to the right page. Amendments and renewals come back with their agreement |
| Tasks | Clause extraction, playbook comparison and structured summaries, judged against reviewed reference answers |
| Tuned against untuned | Same test contracts, same retrieval, same instructions |
| External against private | Only on data that is eligible to leave |
| Permissions | A user without access neither retrieves a passage nor receives an answer built on it. Answers into shared channels are checked first |
| Routing | A private request never reaches an external provider, including when the private model is down |
| Capacity | Memory use and response time with the base model and the adapter loaded together |

## Acceptance criteria

The criteria were fixed as numbers before the pilot started, so that results could not move the bar afterwards. They set:

- the score each task has to reach, and who judges it
- how much better the tuned model has to be than the baseline to justify deploying an adapter
- the tolerated error rate in extraction, with numbers and negations treated strictly
- the response time that is acceptable in Slack

Permission and routing tests tolerate no failures.
