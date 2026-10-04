# Rollout

The assistant was built as a phased pilot. Each phase had an exit check, and each could end with the decision to stop.

| Phase | Work | Exit check |
| --- | --- | --- |
| 1 | Processed 20 varied contracts: inventory, local extraction, quality checks | Extraction quality is known for numbers, negations, tables and scanned pages |
| 2 | Established retrieval and the baseline evaluation | The untuned model with retrieval and the playbook has a measured baseline |
| 3 | Connected private inference to Hermes Agent and Slack | Authorised users get cited answers in Slack, permission and routing tests pass, and no request reaches an external model |
| 4 | Fine-tuned a targeted LoRA adapter | Tuned and untuned are compared on unseen contracts, and the adapter goes into service only if it does better |
| 5 | Opened controlled external routing | Only eligible data goes out, to named models and providers, with no silent fallback |
