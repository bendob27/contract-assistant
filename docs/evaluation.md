# Evaluation

No result is assumed in advance. The adapter may not beat the untuned model. An external model may not beat the private one. The evaluation exists to find out. It decides what goes into service. Its measured results are not published in this repository.

## Questions it answers

| # | Question | What it decides |
| --- | --- | --- |
| 1 | Is the extracted text reliable enough to build on? | Whether extraction needs work first |
| 2 | How good is the untuned model with retrieval and the calculator? | The baseline that every later result is compared with |
| 3 | Does the adapter beat that baseline on reserved cases? | Whether the adapter goes into service |
| 4 | On material that may leave, does a leading hosted model do better? | Whether an external route is opened for that kind of task |
| 5 | Do permissions and routing hold under test? | Whether the assistant may be used in Slack |
| 6 | What does an acceptable recommendation cost? | Whether the work continues |

## Test data

- Complete agreement families are reserved before any training example or playbook lesson is derived. A family is an agreement with its amendments and renewals.
- Related agreements and near-duplicate templates stay on the same side of the split, so no test case leaks into training.
- A final test set stays untouched until the final comparison. A separate development set is used while iterating.
- Each historical test uses only the information that was available on its decision date.

## Comparisons

| Comparison | What is held equal |
| --- | --- |
| Adapter against the untuned model | The base model, the evidence, retrieval, the calculator and the instructions |
| Leading hosted model against the private model | Runs only on material permitted for external processing |
| New model or adapter against the one in service | The reserved cases and the reviewers |

## Blind review

Commercial reviewers compare outputs without knowing which model produced them. They score seven criteria.

| Criterion | What the reviewer asks |
| --- | --- |
| Grounded precedents | Are the cited agreements comparable, and do the citations hold? |
| Milestone quality | Is each milestone tied to value, and can it be verified? |
| Justified amounts | Does every amount trace back to the calculator's inputs? |
| Numerical accuracy | Are the figures right? |
| Unsupported claims | Does any statement lack evidence? |
| Missing information | Does the memo flag what is missing instead of guessing? |
| Time to decision | How long does it take to reach an acceptable decision? |

## System checks

| Area | Checks |
| --- | --- |
| Extraction | Amounts, milestone conditions, dates, negations, tables and scanned pages, compared with the original document |
| Retrieval | The right agreement and passage are found. The source reference points to the right place. Amendments and renewals come back with their agreement |
| Calculator | The same inputs always give the same scenarios. Every figure in a memo matches a calculator output |
| Permissions | A user without access neither retrieves a passage nor receives an answer built on it. Answers into shared channels are checked first |
| Routing | A private request never reaches an external provider, including when the private model is down. The external-request tool rejects any request that fails its checks |
| Capacity | Memory use and response time, with the base model and the adapter loaded together on the one GPU |

## Acceptance criteria

The pass marks are fixed as numbers before the final results are seen, so the results cannot move the bar. They set:

- the score each criterion has to reach, and who judges it
- how much better a new model or adapter has to be than the one in service
- the tolerated error rate in extraction, with amounts, dates and negations treated strictly
- the response time that is acceptable in Slack

The adapter goes into service only if it improves useful performance, or if it reaches acceptable quality at a demonstrably lower total cost. The baseline stays available for rollback. Permission and routing tests tolerate no failures.

## Cost

Cost is measured per acceptable recommendation. It includes the allocated server rental, training runs, storage, external-model usage, data preparation, maintenance and human review. Workloads are compared like for like, at the same quality bar. Lower inference cost alone does not show that the work pays off.

## What this cannot prove

Offline review shows whether recommendations are better supported and easier to use. It cannot show what a different set of terms would have returned. New agreements are therefore tracked from signing, with consistent outcome definitions and observation periods. Stronger claims about cause and effect need a suitable comparison group or a prospective evaluation design.
