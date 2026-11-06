# Rollout

The assistant is built in five phases. Each phase has an exit check. Each can end with the decision to stop.

| Phase | Work | Exit check |
| --- | --- | --- |
| 1 | Define the objective, the value metric, the observation period, the review rubric and the decision owner. Inventory the agreements, and find the comparable cohorts and the outcome records that exist | The value definition is approved, and the available data is known |
| 2 | Reserve the evaluation cases. Build the private evidence library, the reviewed case table, the calculation rules and the first playbook | The reserved cases are locked, and the case table and the calculator pass review |
| 3 | Choose the base model with a feasibility and quality test on representative cases. Run the untuned model with retrieval and the calculator through the private service and Slack | Tool calling works end to end, permission and routing tests pass, and the baseline is measured |
| 4 | Train the LoRA adapter privately on reviewed examples. Compare it with the baseline on the reserved cases, and record costs and reviewer effort | The adapter meets the pass marks fixed in advance, or it stays out of service |
| 5 | Pilot with human approval. Collect structured corrections, track actual outcomes and version every change | A person approves every term, and the previous model, adapter, prompts and calculation rules can be restored |

A person approves every proposal in every phase. Corrections feed the next training run. Outcomes are tracked from signing.
