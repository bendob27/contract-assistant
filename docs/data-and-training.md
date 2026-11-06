# Data and training

The deal specialist is a LoRA adapter on Qwen3.8-27B. It learns how to apply the approved assessment method. Retrieval and the calculator, not the adapter, supply past agreements, outcomes and amounts.

## Define value first

The value metric, the observation period and the acceptable trade-offs are agreed before any case is labelled a success. Candidate measures are attributable revenue or contribution, or another agreed commercial outcome.

- A delivered milestone is not valuable in itself.
- The value the business captures is kept apart from the counterparty's overall success.
- No source of value is counted twice.

Two questions stay separate. The first is what the business can justify paying. The second is the least the counterparty would accept. A successful agreement does not show that a smaller amount would have worked. It does not show that a larger amount would have created more value. Nor does it show what would have happened without the agreement.

## Case records

An agreement family is an agreement together with its amendments and renewals. Each family becomes a case record with four parts.

| Part | What it holds |
| --- | --- |
| Decision-time context | The proposal, what was known about the counterparty, the expected costs, the alternatives, and the forecasts available when the decision was made |
| Terms and negotiation | The first offer and any counteroffers, the cap, the payment schedule, the milestone definitions, the verification rules, the duration, amendments and termination terms |
| Actual outcomes | Amounts paid, which milestones were delivered and when, measured commercial results, internal support costs, renegotiations, and documented reasons for non-delivery |
| Traceability | Agreement-family IDs, dates, page references, access rights, currency and valuation dates, the observation period and the quality of the evidence |

Unfinished cases stay distinct from failures. Outcomes come from operating records, because a signed agreement does not show what happened next. Successes, partial successes and failures all enter the case table where they are genuinely comparable.

## Corpus

- A few hundred past agreements, each linked to its amendments and renewals.
- Extraction, the case table and training all stay on the server. Models are downloaded from Hugging Face Hub directly onto it, and no agreement is uploaded anywhere.

## What the specialist learns

The adapter learns a method, not a set of deals. Given a proposal and the permitted evidence, it:

1. selects comparable precedents and explains how they differ
2. finds milestones that could be met without creating value
3. compares two or three structures
4. explains the calculator's scenarios
5. states what is uncertain and what to ask in negotiation

Training on the successful agreements alone would not teach it which terms caused the value. It would not teach the smallest amount the counterparty would have accepted either.

## Training examples

- The input is a proposal and the evidence the specialist is permitted to see.
- The target is the approved assessment and recommendation.
- Rejected structures are included, with the reason another structure was preferred.
- AI may draft an example. Someone who knows the agreements reviews and approves it.
- Expert corrections from live use become reviewed examples for a later training run. Feedback never updates the live model directly.

## What lives where

| Component | Holds | Changes when |
| --- | --- | --- |
| Retrieval | Agreements, outcome records and the current playbook | A new agreement or outcome is ingested and indexed |
| Calculator | Amounts, scenarios and their versioned assumptions | The business approves new assumptions |
| Adapter | How to use the evidence and the scenarios consistently | A later training run passes evaluation |

A new agreement needs ingestion, not retraining.

## Sequence

The order matters. Evaluation cases are reserved before any example is written. The baseline, the score of the untuned model, is measured before any fine-tuning.

1. **Define** the objective, the value metric, the observation period, the review rubric and the decision owner.
2. **Inventory** the agreements, and link each one to its amendments and renewals.
3. **Reserve** the evaluation cases as complete agreement families. Related agreements and near-duplicates stay on the same side of the split, so no test case leaks into training.
4. **Extract** with Docling on the server. Check amounts, milestone conditions, dates, negations, tables and scanned pages against the originals.
5. **Build** the case table, and join the outcomes from operating records.
6. **Agree** the calculation rules and the first version of the playbook.
7. **Write** training examples from the remaining families, and have each one reviewed.
8. **Measure** the baseline: the untuned model with retrieval and the calculator.
9. **Train** the LoRA adapter on the server.
10. **Compare** the adapter with the baseline on the reserved cases. The adapter goes into service only if it passes.

## Rules for training data

- Signed terms are historical outcomes, not necessarily the terms the business would choose now. Examples are written against the current playbook.
- Winning agreements alone do not show which terms caused the value.
- Many examples generated from one agreement are not independent evidence. With a few hundred agreements, data quality and careful scope matter more than volume.
- An adapter can retain restricted material that retrieval access controls would have protected. Training uses only material that every user of the adapter may see. Restricted material is served through retrieval, where access controls apply.

## Training and serving

Training produces an adapter. vLLM loads it next to the matching base model, without a second copy of the model. Training can need more GPU memory than serving. It runs as a separate job and is not assumed to share the live server. Tool calling through Hermes Agent is tested with the adapter loaded before the adapter goes into service.

## Toolchain

| Tool | Role |
| --- | --- |
| [Hugging Face Hub](https://huggingface.co/docs/hub) | Source of the model weights |
| [Transformers](https://huggingface.co/docs/transformers) | Loads the model and the tokeniser for training |
| [Datasets](https://huggingface.co/docs/datasets) | Holds the training, development and test examples |
| [TRL](https://huggingface.co/docs/trl) | Runs the supervised fine-tuning |
| [PEFT](https://huggingface.co/docs/peft) | LoRA: the base weights stay frozen and a small adapter is trained |
| [bitsandbytes](https://huggingface.co/docs/bitsandbytes) | Optional. Loads the base model quantised for QLoRA, the lower-memory variant of LoRA |
| [Accelerate](https://huggingface.co/docs/accelerate) | Device placement and mixed precision |
| [PyTorch](https://docs.pytorch.org/docs/stable/index.html) and [NVIDIA CUDA](https://docs.nvidia.com/cuda/) | Perform the training computations on the GPU |
