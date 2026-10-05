# Data and training

The contract specialist in service is a LoRA adapter on Mistral Small 4, the base model. It replaced an earlier adapter on Ministral 3 14B Instruct, which is retired.

## Corpus

- About 500 contracts signed over three years, with each agreement linked to its amendments and renewals.
- Extraction and fine-tuning run on the server, and the searchable contracts are held there. Models are downloaded from Hugging Face Hub directly onto it, so no contract is uploaded to Hugging Face.

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

The reviewed examples and the test set are the reference for every comparison, including Mistral Small 4 against Ministral 3 14B Instruct.

## Rules for training data

- Signed terms are historical outcomes, not necessarily our preferred negotiating positions. This is why examples are written against the current playbook.
- Train only on examples approved for the specialist's audience. Retrieval permissions cannot prevent a model from recalling sensitive material it learned during training. Material restricted to some users stays out of training. It is served through retrieval, where access controls apply.

## Why retrieval stays

Retrieval is maintained alongside fine-tuning. New contracts become searchable without retraining, answers can cite the clause they rest on, and access controls apply to each request. Fine-tuning is for how the specialist handles narrow tasks. It is not where the contracts are stored.

## Toolchain

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
