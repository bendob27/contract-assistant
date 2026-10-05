# Data and training

Mistral Small 4 with retrieval replaced Ministral 3 14B Instruct and its fine-tuned LoRA adapter, so no fine-tuned model is in service. The data work below still feeds retrieval and evaluation. The fine-tuning steps describe the retired adapter.

## Corpus

- About 500 contracts signed over three years, with each agreement linked to its amendments and renewals.
- Extraction runs on the server, and the searchable contracts are held there. Model weights are downloaded from Hugging Face Hub directly onto it, so no contract is uploaded to Hugging Face.

## Sequence

The order matters. The split comes before any examples are generated, and the baseline comes before any fine-tuning.

1. **Inventory.** List the contracts and link each agreement to its amendments and renewals.
2. **Extract.** Run Docling locally and preserve clause references, and page references for PDFs.
3. **Check extraction quality**, especially numbers, negations, tables and scanned pages.
4. **Split** into training, validation and test sets before generating examples. Related contracts and near-duplicates stay in the same set.
5. **Write examples.** Reviewed input and output pairs for narrow tasks such as clause extraction, playbook comparison and structured summaries.
6. **Review.** AI may draft the examples. Someone who knows the contracts reviews them.
7. **Baseline.** Benchmark the untuned model with retrieval and the current playbook.
8. **Fine-tune** a targeted LoRA adapter on the reviewed examples.
9. **Compare** the tuned and the untuned model on unseen contracts. The adapter goes into service only if it does better.

Steps 8 and 9 produced the adapter for Ministral 3 14B Instruct, now retired. The reviewed examples and the test set remain the reference for every model comparison.

## Rules for training data

These rules governed the training data for the retired adapter.

- Signed terms are historical outcomes, not necessarily our preferred negotiating positions. This is why examples are written against the current playbook.
- Train only on examples approved for the adapter's audience. Retrieval permissions cannot prevent a model from recalling sensitive material it learned during training. Material restricted to some users stays out of training. It is served through retrieval, where access controls apply.

## Why retrieval carries the contracts

New contracts become searchable without retraining, answers can cite the clause they rest on, and access controls apply to each request. With no fine-tuned model in service, nothing from the contracts sits in the model's weights, so the model cannot recall a contract a user may not see.

## Fine-tuning toolchain

Used for the retired adapter.

| Tool | Role |
| --- | --- |
| [Hugging Face Hub](https://huggingface.co/docs/hub) | Source of the model weights |
| [Transformers](https://huggingface.co/docs/transformers) | Loads the model and the tokeniser for training |
| [Datasets](https://huggingface.co/docs/datasets) | Holds the training, validation and test examples |
| [TRL](https://huggingface.co/docs/trl) | Runs the supervised fine-tuning |
| [PEFT](https://huggingface.co/docs/peft) | LoRA: the base weights stay frozen and a small adapter is trained |
| [bitsandbytes](https://huggingface.co/docs/bitsandbytes) | Optional. Loads the base model quantised for QLoRA, the lower-memory variant of LoRA |
| [Accelerate](https://huggingface.co/docs/accelerate) | Device placement and mixed precision |
| [PyTorch](https://docs.pytorch.org/docs/stable/index.html) and [NVIDIA CUDA](https://docs.nvidia.com/cuda/) | Perform the training computations on the GPU |
