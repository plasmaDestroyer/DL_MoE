# Viva Prep: DL_MoE Project Details

## 1. Project Identity

- Project name: `DL_MoE`.
- Full title from report: `Frozen-Expert MoE for Code-Mixed Language Modeling for Indian Social Media`.
- Main idea: token-level frozen-expert Mixture of Experts for Hindi-English code-mixed NLP.
- Core model name in report: `FE-MoE`, Frozen-Expert Mixture of Experts.
- Target text type: Indian social media Hinglish / Hindi-English code-mixed text.
- Main tasks used in sweep: Language Identification (`lid`) and POS tagging (`pos`).
- NER code exists, but NER is disabled in main sweep.
- Main code directory: `03_code/`.
- Main results directory: `05_results/`.
- Dataset docs directory: `04_data/`.
- Report directory: `02_report/`.
- Claim docs directory: `07_claims/`.

## 2. Problem Statement

- Hindi-English code-mixing means alternating Hindi and English inside one utterance.
- Example style: `yaar kal ka presentation ready hai kya`.
- Standard monolingual NLP models struggle because tokens can follow Hindi syntax, English syntax, or mixed usage.
- Romanized Hindi has high spelling variation.
- Same Hindi word can appear as `nahi`, `nahin`, `nhi`, etc.
- Code-mixed text needs token-level language sensitivity.
- A sentence-level model decision is too coarse because language can switch word by word.

## 3. Main Research Question

- Whether two frozen pretrained experts can be combined by a small learned router for code-mixed sequence labeling.
- Whether learned routing can approach full fine-tuning on LID while training far fewer parameters.
- Whether router weights become language-aware without direct supervision on the router.
- Whether the same frozen-router idea works for syntactic tasks like POS.

## 4. Main Claim

- Best MoE variant for LID: `R2`, MLP router with temperature `tau=0.1`.
- Saved result: `R2` LID weighted F1 = `0.8898 +/- 0.0065`.
- Fully fine-tuned HingBERT baseline: `B1` LID weighted F1 = `0.8965 +/- 0.0023`.
- LID gap between best MoE and full fine-tuning: about `0.0067`, or `0.67 percentage points`.
- Best MoE uses frozen experts and trains only router plus task head.
- POS does not work well with frozen MoE.
- Best POS result overall: `B1`, accuracy = `0.9109 +/- 0.0011`.
- Best MoE POS result in saved results: `R1`, accuracy = `0.6776 +/- 0.0150`.

## 5. Important Result Source Warning

- `05_results/main_results.csv` and `05_results/metrics/aggregated.json` agree.
- The top-level `README.md` has a result table that differs from saved result files for some rows.
- The saved result files should be treated as authoritative.
- Example discrepancy:
  - README says `B2` LID = `86.41 +/- 0.08`.
  - Saved CSV says `B2` LID = `80.55 +/- 0.06`.
  - README says `R1` POS = `69.37 +/- 0.15`.
  - Saved CSV says `R1` POS = `67.76 +/- 1.50`.
- The report `02_report/DL_REPORT.tex` matches the saved CSV for main result values.

## 6. Repository Structure

- `README.md`: main project documentation.
- `DL_MoE_Presentation 1.pdf`: 12-slide project presentation deck.
- `01_admin/`: administrative submission artifact; contains PDF in current repo.
- `02_report/DL_REPORT.tex`: IEEE-style project report source.
- `02_report/DL_REPORT.pdf`: compiled report.
- `02_report/references.bib`: bibliography.
- `02_report/fig_alpha_by_lang.png`: report figure.
- `02_report/fig_disagreement.png`: report figure.
- `02_report/fig_switch_points.png`: report figure.
- `03_code/configs.py`: constants, hyperparameters, labels, experiment registry.
- `03_code/data.py`: dataset loading, cleaning, tokenization, dataloaders.
- `03_code/models.py`: experts, MoE, baselines, word alignment, model factory.
- `03_code/routers.py`: MLP, GRU, CNN router implementations.
- `03_code/training.py`: training loop, evaluation, early stopping, checkpoint save/load.
- `03_code/analysis.py`: router interpretability analyses.
- `03_code/main.py`: CLI entrypoint for train/eval/analysis/sweep.
- `03_code/api.py`: Flask API and ensemble inference backend.
- `03_code/setup_data.py`: downloads models and datasets for offline use.
- `03_code/tests/test_alignment.py`: unit tests for alignment.
- `03_code/website/`: static analyzer/results/analysis pages.
- `03_code/scripts/`: shell wrappers for train/eval/sweep/demo.
- `04_data/data_description.md`: dataset summary.
- `04_data/dataset_links.txt`: dataset and model URLs.
- `04_data/sample_inputs/`: sample LID and POS inputs.
- `05_results/checkpoints/`: saved `.pt` trainable weights.
- `05_results/metrics/`: per-run metrics and aggregated metrics.
- `05_results/logs/`: per-epoch CSV logs.
- `05_results/figures/`: interpretability figures and stats JSON files.
- `05_results/main_results.csv`: main result table.
- `05_results/ablations.csv`: ablation result table.
- `07_claims/claimed_contribution.md`: claimed original/modification work.
- `07_claims/prior_work_basis.md`: prior work basis.

## 7. Dependencies

- `torch>=2.0.0`.
- `transformers>=4.35.0`.
- `datasets>=2.14.0`.
- `numpy>=1.24.0`.
- `scikit-learn>=1.3.0`.
- `seqeval>=1.2.2`.
- `matplotlib>=3.7.0`.
- `seaborn>=0.12.0`.
- `flask>=3.0.0`.
- `flask-cors>=4.0.0`.
- `pytest>=7.4.0`.

## 8. Dataset Details

- Dataset acquisition, raw formats, cleaning, splits, sample inputs, and dataset analysis are now separated into `DATASET_DETAILS.md`.
- Short status:
  - `models_and_data/` is not present in the current workspace.
  - Full raw/cleaned dataset files are not locally available.
  - Existing repo contains dataset docs, sample inputs, metrics, logs, checkpoints, figures, and analysis stats.
  - Exact raw and cleaned row counts require running `03_code/setup_data.py` and the loaders.
- Presentation deck was available and reviewed: `DL_MoE_Presentation 1.pdf`.

## 9. Pretrained Experts

- Expert 1: HingBERT.
- HingBERT local path in code: `models_and_data/hing-bert`.
- HingBERT source ID: `l3cube-pune/hing-bert`.
- HingBERT role: Hindi-English / Hinglish specialist.
- HingBERT tokenizer type: WordPiece-style BERT tokenizer.
- HingBERT hidden size used by model: `768`.
- Expert 2: RoBERTa-base.
- RoBERTa local path in code: `models_and_data/roberta-base`.
- RoBERTa source ID: `roberta-base`.
- RoBERTa role: English generalist.
- RoBERTa tokenizer type: BPE tokenizer with `add_prefix_space=True`.
- RoBERTa hidden size used by model: `768`.
- Both experts are loaded with `transformers.AutoModel.from_pretrained`.
- Both experts return `last_hidden_state`.
- Last hidden state shape before alignment: `(batch, subtoken_seq_len, 768)`.

## 10. Why Frozen Experts

- Full fine-tuning updates many parameters.
- Freezing preserves pretrained representations.
- Freezing reduces trainable parameters.
- Freezing prevents gradient computation through experts for MoE and frozen baselines.
- Harder problem than fine-tuning because the router cannot change expert representations.
- Router must handle representation mismatch between two independently trained encoders.
- Router must handle tokenizer mismatch between WordPiece and BPE.
- Router must infer language/task routing only from downstream loss.

## 11. Model Architecture

- Input sentence is treated as a list of words.
- Same word list is sent to HingBERT tokenizer and RoBERTa tokenizer.
- Both tokenizers use `is_split_into_words=True`.
- Each expert produces subtoken hidden states.
- Subtoken hidden states are averaged back to original word positions.
- HingBERT aligned representation: `h_hing`, shape `(B, T, 768)`.
- RoBERTa aligned representation: `h_rob`, shape `(B, T, 768)`.
- Router outputs scalar `alpha` per word.
- Alpha shape for token-level routing: `(B, T, 1)`.
- Alpha meaning in model code:
  - `alpha=1`: full HingBERT representation.
  - `alpha=0`: full RoBERTa representation.
- Blending formula:
  - `blended = alpha * h_hing + (1 - alpha) * h_rob`.
- Task head:
  - `nn.Linear(768, num_labels)`.
- Output logits shape:
  - `(B, T, num_labels)`.
- Model also computes expert disagreement:
  - `l2_disagreement = torch.norm(h_hing - h_rob, dim=-1)`.
- L2 disagreement shape:
  - `(B, T)`.

## 12. Subtoken-to-Word Alignment

- Function: `align_subtokens_to_words(hidden, word_ids, max_words)`.
- Location: `03_code/models.py`.
- Input hidden shape: `(seq_len, D)`.
- `word_ids` comes from tokenizer `word_ids()`.
- Special tokens have `word_id=None`.
- Padding tokens have `word_id=None`.
- For each subtoken with valid word id:
  - Add hidden vector to output for that word.
  - Increment count for that word.
- After loop:
  - Divide each non-empty word vector by subtoken count.
- Empty padded word positions remain zero.
- Output shape: `(max_words, D)`.
- Alignment handles different subtoken counts from HingBERT and RoBERTa.
- Conservative word count after tokenization:
  - `num_words = min(hing_max, rob_max, len(words))`.
- Labels are truncated to `num_words`.
- Words stored for analysis are truncated to `num_words`.

## 13. Router Architectures

### RouterMLP

- Class: `RouterMLP`.
- Used in: `R1` to `R7`, `A2`, `A3`.
- Token-level input shape: `(B, T, input_dim)`.
- Sentence-level input shape: `(B, input_dim)`.
- Input dim:
  - `1536` when router sees both experts.
  - `768` when router sees only one expert.
  - `1536` for sentence-level routing.
- Hidden dim in code: `256`.
- Layers:
  - `Linear(input_dim, 256)`.
  - `GELU`.
  - `Linear(256, 1)`.
  - Temperature-scaled sigmoid.
- Output:
  - `alpha = sigmoid(logit / tau)`.
- No sequence/neighborhood context in token-level MLP.

### RouterGRU

- Class: `RouterGRU`.
- Used in: `R8`.
- Input shape: `(B, T, input_dim)`.
- Projection:
  - `Linear(input_dim, 128)`.
- GRU:
  - `nn.GRU(input_size=128, hidden_size=64, bidirectional=True, batch_first=True)`.
- Output sequence dim after BiGRU:
  - `128`, because `2 * hidden_size = 128`.
- Head:
  - `Linear(128, 1)`.
- Uses `pack_padded_sequence`.
- Requires `word_lens`.
- Handles variable-length word sequences.
- Note:
  - README/report prose describes GRU hidden sizes differently in places.
  - Code is authoritative: `proj_dim=128`, `hidden_dim=64`, bidirectional output `128`.

### RouterCNN

- Class: `RouterCNN`.
- Used in: `R9`.
- Input shape: `(B, T, input_dim)`.
- Transposes to `(B, input_dim, T)` for `Conv1d`.
- First convolution:
  - `Conv1d(input_dim, 64, kernel_size=5, padding=2)`.
- Activation:
  - `GELU`.
- Second convolution:
  - `Conv1d(64, 1, kernel_size=1)`.
- Output transposed back to `(B, T, 1)`.
- Kernel size `5` means local window of 5 words.
- Padding `2` preserves sequence length.

## 14. Routing Modes

- Soft token-level routing:
  - Default mode.
  - Continuous alpha per token.
  - Fully differentiable.
- Hard token-level routing:
  - Experiment `R7`.
  - Uses threshold `alpha_soft >= 0.5`.
  - Training uses straight-through estimator:
    - `alpha_hard - alpha_soft.detach() + alpha_soft`.
  - Evaluation returns hard binary alpha.
- Sentence-level routing:
  - Experiment `R6`.
  - Uses CLS hidden states from both experts.
  - Concatenates `h_hing_sub[:, 0, :]` and `h_rob_sub[:, 0, :]`.
  - Router produces one alpha per sentence.
  - Alpha is broadcast to all word positions.
  - Supported only with MLP router.

## 15. Baseline Models

### B1: Fine-Tuned HingBERT

- Config: `{"model_mode": "hingbert", "frozen": False}`.
- Expert: HingBERT.
- Expert is trainable.
- Task head is trainable.
- Used as full fine-tuning upper-bound baseline.
- Reported trainable parameter count in report: about `85M`.
- Checkpoints for B1 are not present in current `05_results/checkpoints/`.
- B1 metric JSON files are present.

### B2: Frozen HingBERT

- Config: `{"model_mode": "hingbert", "frozen": True}`.
- Expert: HingBERT.
- Expert frozen.
- Only task head trainable.
- Tests quality of frozen HingBERT representation plus linear classifier.

### B3: Frozen RoBERTa

- Config: `{"model_mode": "roberta", "frozen": True}`.
- Expert: RoBERTa.
- Expert frozen.
- Only task head trainable.
- Tests quality of frozen RoBERTa representation plus linear classifier.

### B4: Fixed Average

- Config: `{"model_mode": "fixed_avg"}`.
- Expert 1: frozen HingBERT.
- Expert 2: frozen RoBERTa.
- Blend: `0.5 * h_hing + 0.5 * h_rob`.
- No learned router.
- Task head trainable.
- Alpha returned as constant `0.5`.
- Tests whether unlearned averaging helps.

## 16. Experiment Registry

- Source: `03_code/configs.py`.
- `B1`: HingBERT full fine-tune.
- `B2`: frozen HingBERT.
- `B3`: frozen RoBERTa.
- `B4`: fixed 50/50 average.
- `R1`: MoE, MLP, `tau=1.0`, both experts as router input.
- `R2`: MoE, MLP, `tau=0.1`.
- `R3`: MoE, MLP, `tau=0.3`.
- `R4`: MoE, MLP, `tau=0.5`.
- `R5`: MoE, MLP, `tau=2.0`.
- `R6`: MoE, MLP, `tau=1.0`, sentence-level routing.
- `R7`: MoE, MLP, `tau=1.0`, hard routing.
- `R8`: MoE, GRU router, `tau=1.0`.
- `R9`: MoE, CNN router, `tau=1.0`.
- `A2`: MoE, MLP, router sees only HingBERT hidden states.
- `A3`: MoE, MLP, router sees only RoBERTa hidden states.
- `A1` is conceptually same as `R1`.

## 17. Exact Trainable Parameter Counts From Code Formulas

- HingBERT and RoBERTa hidden size: `768`.
- MLP router with both experts:
  - `Linear(1536,256)`: `1536*256 + 256 = 393472`.
  - `Linear(256,1)`: `256*1 + 1 = 257`.
  - Router total: `393729`.
- MLP router with one expert:
  - `Linear(768,256)`: `768*256 + 256 = 196864`.
  - `Linear(256,1)`: `257`.
  - Router total: `197121`.
- CNN router with both experts:
  - `Conv1d(1536,64,k=5)`: `1536*64*5 + 64 = 491584`.
  - `Conv1d(64,1,k=1)`: `64*1*1 + 1 = 65`.
  - Router total: `491649`.
- GRU router with both experts:
  - Projection `Linear(1536,128)`: `196736`.
  - BiGRU total from PyTorch parameterization: `74496`.
  - Head `Linear(128,1)`: `129`.
  - Router total: `271361`.
- LID task head:
  - `Linear(768,3)`: `768*3 + 3 = 2307`.
- POS task head:
  - `Linear(768,14)`: `768*14 + 14 = 10766`.
- NER task head:
  - `Linear(768,7)`: `768*7 + 7 = 5383`.
- R1/R2/R3/R4/R5/R6/R7 LID trainable count from code: `393729 + 2307 = 396036`.
- R1/R2/R3/R4/R5/R6/R7 POS trainable count from code: `393729 + 10766 = 404495`.
- A2/A3 LID trainable count from code: `197121 + 2307 = 199428`.
- A2/A3 POS trainable count from code: `197121 + 10766 = 207887`.
- R8 LID trainable count from code: `271361 + 2307 = 273668`.
- R8 POS trainable count from code: `271361 + 10766 = 282127`.
- R9 LID trainable count from code: `491649 + 2307 = 493956`.
- R9 POS trainable count from code: `491649 + 10766 = 502415`.
- B2/B3 LID trainable count from code: `2307`.
- B2/B3 POS trainable count from code: `10766`.
- B4 LID trainable count from code: `2307`.
- B4 POS trainable count from code: `10766`.
- Report says router is approximately `200K`.
- That is exact for single-expert-input MLP ablations, but full MLP MoE is about `396K` including head.

## 18. Task Labels

### LID Labels

- Task key: `lid`.
- Number of classes: `3`.
- Labels:
  - `hi`: Hindi.
  - `en`: English.
  - `ot`: Other.
- Metric: weighted F1.

### POS Labels

- Task key: `pos`.
- Number of classes: `14`.
- Labels:
  - `NOUN`.
  - `PROPN`.
  - `VERB`.
  - `ADJ`.
  - `ADV`.
  - `ADP`.
  - `PRON`.
  - `DET`.
  - `CONJ`.
  - `PART`.
  - `PRON_WH`.
  - `PART_NEG`.
  - `NUM`.
  - `X`.
- Metric: token-level accuracy.

### NER Labels

- Task key: `ner`.
- Number of classes: `7`.
- Labels:
  - `O`.
  - `B-PERSON`.
  - `I-PERSON`.
  - `B-ORGANIZATION`.
  - `I-ORGANIZATION`.
  - `B-LOCATION`.
  - `I-LOCATION`.
- Metric: entity-level F1 using `seqeval`.
- NER is supported by code but excluded from main tasks list.

## 19. Dataset Reference

- Full dataset acquisition, original formats, cleaning rules, split logic, tokenization-time filtering, label distributions, and sample inputs are documented in `DATASET_DETAILS.md`.
- Key summary:
  - LID uses COMI-LINGUA from HuggingFace.
  - POS uses `models_and_data/finalData.tsv` from a GitHub raw TSV URL.
  - NER uses `models_and_data/annotatedData.csv` from a GitHub raw CSV URL, but NER is excluded from the main sweep.
  - `models_and_data/` is not present locally, so exact raw/cleaned counts need data setup before verification.

## 24. Training Hyperparameters

- Source: `03_code/configs.py`.
- Optimizer: AdamW.
- Learning rate: `2e-4`.
- Batch size: `32`.
- Max epochs: `20`.
- Early stopping patience: `3`.
- Max sequence length: `128`.
- Warmup fraction: `0.1`.
- Gradient clipping norm: `1.0`.
- Seeds: `[42, 123]`.
- Active sweep tasks: `["lid", "pos"]`.
- NER can be enabled by adding `"ner"` to `TASKS`.

## 25. Training Loop

- Class: `Trainer`.
- Trainable parameters are selected by `p.requires_grad`.
- Raises error if no trainable parameters.
- Loss function:
  - `nn.CrossEntropyLoss(ignore_index=-100)`.
- Scheduler:
  - `transformers.get_linear_schedule_with_warmup`.
- Total steps:
  - `len(train_dl) * max_epochs`.
- Warmup steps:
  - `int(total_steps * 0.1)`.
- Each epoch:
  - train one epoch.
  - evaluate on validation set.
  - append epoch, train loss, validation metric to log.
- Early stopping:
  - monitors validation metric.
  - saves best trainable-state copy in memory.
  - stops after `patience` non-improving epochs.
  - restores best trainable weights after training.
- Logs:
  - CSV columns `epoch`, `train_loss`, `val_metric`.
  - Stored in `05_results/logs/{exp_id}-{task}-s{seed}.csv`.

## 26. Checkpointing

- Checkpoints save only trainable parameters.
- Checkpoint keys:
  - `trainable`.
  - `metrics`.
  - `exp_config`.
  - `task`.
- Checkpoint path:
  - `05_results/checkpoints/{exp_id}-{task}-s{seed}.pt`.
- Metrics path:
  - `05_results/metrics/{exp_id}-{task}-s{seed}.json`.
- Checkpoint loader copies saved trainable tensors into matching model parameters.
- Shape mismatches are skipped with warning.
- Frozen expert weights are not saved in MoE checkpoints.
- Frozen expert weights must exist locally under `models_and_data/`.
- This explains why MoE checkpoint files are small.

## 27. Evaluation Metrics

- Function: `evaluate`.
- Model is set to eval mode.
- Uses `torch.no_grad()`.
- Predictions:
  - `logits.argmax(-1)`.
- Only first `num_words` positions are evaluated.
- LID:
  - weighted F1 from `sklearn.metrics.f1_score`.
  - `average="weighted"`.
  - `zero_division=0`.
- POS:
  - accuracy from `sklearn.metrics.accuracy_score`.
- NER:
  - entity-level F1 from `seqeval.metrics.f1_score`.
  - Converts ids back to BIO label strings.

## 28. Main Results From Saved CSV

| ID | Description | LID mean | LID std | LID s42 | LID s123 | POS mean | POS std | POS s42 | POS s123 |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| B1 | HingBERT fine-tuned | 0.8965 | 0.0023 | 0.8942 | 0.8987 | 0.9109 | 0.0011 | 0.9099 | 0.9120 |
| B2 | HingBERT frozen | 0.8055 | 0.0006 | 0.8050 | 0.8061 | 0.6523 | 0.0025 | 0.6548 | 0.6499 |
| B3 | RoBERTa frozen | 0.8148 | 0.0001 | 0.8149 | 0.8147 | 0.6318 | 0.0031 | 0.6349 | 0.6287 |
| B4 | Fixed 50/50 avg | 0.8041 | 0.0031 | 0.8009 | 0.8072 | 0.6110 | 0.0012 | 0.6098 | 0.6122 |
| R1 | MoE MLP tau=1.0 | 0.8437 | 0.0321 | 0.8758 | 0.8117 | 0.6776 | 0.0150 | 0.6926 | 0.6626 |
| R2 | MoE MLP tau=0.1 | 0.8898 | 0.0065 | 0.8833 | 0.8963 | 0.6699 | 0.0154 | 0.6545 | 0.6853 |
| R3 | MoE MLP tau=0.3 | 0.8886 | 0.0029 | 0.8858 | 0.8915 | 0.6699 | 0.0125 | 0.6824 | 0.6574 |
| R4 | MoE MLP tau=0.5 | 0.8843 | 0.0011 | 0.8832 | 0.8854 | 0.6691 | 0.0097 | 0.6788 | 0.6595 |
| R5 | MoE MLP tau=2.0 | 0.8440 | 0.0324 | 0.8764 | 0.8116 | 0.6679 | 0.0028 | 0.6650 | 0.6707 |
| R6 | MoE MLP sentence-level | 0.8120 | 0.0004 | 0.8124 | 0.8116 | 0.6644 | 0.0018 | 0.6626 | 0.6662 |
| R7 | MoE MLP hard routing | 0.8758 | 0.0010 | 0.8768 | 0.8748 | 0.6520 | 0.0022 | 0.6497 | 0.6542 |
| R8 | MoE BiGRU router | 0.8661 | 0.0063 | 0.8598 | 0.8724 | 0.6422 | 0.0217 | 0.6640 | 0.6205 |
| R9 | MoE CNN router k=5 | 0.8829 | 0.0012 | 0.8817 | 0.8841 | 0.6678 | 0.0001 | 0.6677 | 0.6679 |
| A2 | MoE MLP HingBERT input only | 0.8606 | 0.0024 | 0.8629 | 0.8582 | 0.6723 | 0.0016 | 0.6707 | 0.6739 |
| A3 | MoE MLP RoBERTa input only | 0.8742 | 0.0008 | 0.8734 | 0.8750 | 0.6705 | 0.0017 | 0.6722 | 0.6688 |

## 29. Main Result Interpretations

- B1 is best on both LID and POS.
- R2 is best MoE on LID.
- R2 nearly matches B1 on LID.
- R2 strongly beats frozen baselines B2, B3, and B4 on LID.
- Learned routing matters for LID.
- Fixed 50/50 average does not help LID.
- RoBERTa frozen B3 beats HingBERT frozen B2 on LID in saved results.
- POS result is different:
  - B1 dominates.
  - All frozen and MoE methods are far below B1.
  - R1 is the highest POS MoE result.
  - POS appears to require task-specific fine-tuning of expert representations.

## 30. Temperature Ablation

- Source: `05_results/ablations.csv`.
- Compared MLP soft token-level models with different tau.
- Lower tau sharpens sigmoid.
- Higher tau softens sigmoid toward 0.5.
- LID:
  - `tau=0.1`, R2: `0.8898`.
  - `tau=0.3`, R3: `0.8886`.
  - `tau=0.5`, R4: `0.8843`.
  - `tau=1.0`, R1: `0.8437`.
  - `tau=2.0`, R5: `0.8440`.
- Temperature conclusion:
  - sharper routing helps LID.
  - very soft routing causes lower mean and higher variance.
- POS:
  - R1 has best POS among tau sweep at `0.6776`.
  - Lower tau does not improve POS.
- Likely viva point:
  - LID benefits from near-discrete language routing.
  - POS needs syntactic adaptation, not only language routing.

## 31. Router Input Ablation

- R1 router input: both `h_hing` and `h_rob`, concatenated.
- A2 router input: only `h_hing`.
- A3 router input: only `h_rob`.
- LID:
  - R1: `0.8437`.
  - A2: `0.8606`.
  - A3: `0.8742`.
- POS:
  - R1: `0.6776`.
  - A2: `0.6723`.
  - A3: `0.6705`.
- Observation:
  - Single-expert router inputs reduce LID variance versus R1.
  - RoBERTa-only router input outperforms HingBERT-only for LID.
  - Full concatenation is not always easiest to optimize.
- Possible explanation:
  - Concatenating two unaligned representation spaces gives router a harder input.
  - RoBERTa representations may encode clearer discriminative structure for routing.

## 32. Router Architecture Ablation

- R1: MLP, tau=1.0.
- R8: BiGRU, tau=1.0.
- R9: CNN, tau=1.0.
- LID:
  - R1: `0.8437`.
  - R8: `0.8661`.
  - R9: `0.8829`.
- POS:
  - R1: `0.6776`.
  - R8: `0.6422`.
  - R9: `0.6678`.
- Conclusion:
  - CNN router performs very well on LID.
  - Local 5-token context is enough for code-mixed language switching.
  - BiGRU underperforms CNN.
  - More sequence capacity is not automatically better.

## 33. Routing Granularity Ablation

- R1: soft token-level routing.
- R7: hard token-level routing.
- R6: soft sentence-level routing.
- LID:
  - R1: `0.8437`.
  - R7: `0.8758`.
  - R6: `0.8120`.
- POS:
  - R1: `0.6776`.
  - R7: `0.6520`.
  - R6: `0.6644`.
- Conclusion:
  - Sentence-level routing is too coarse for code-mixing.
  - Token-level routing is important.
  - Hard routing helps LID relative to R1 but hurts POS.
  - Soft routing preserves useful blending for ambiguous tokens.

## 34. NER Results

- NER code exists.
- NER labels are configured.
- NER loader exists.
- NER metric exists.
- NER is excluded from `TASKS`.
- Aggregated metrics include some NER baseline/custom entries:
  - B2 NER mean: `0.0`.
  - B3 NER mean: `0.002225`.
  - B4 NER mean: `0.008757`.
  - R1 NER mean: `0.0`.
- Project conclusion:
  - frozen experts fail on NER.
  - named-entity boundary detection needs task-specific adaptation.

## 35. Interpretability Analyses

- Source: `03_code/analysis.py`.
- Main object extracted:
  - per-word alpha values.
- Extracted record fields:
  - `sentence_id`.
  - `position`.
  - `word`.
  - `label`.
  - `alpha`.
  - `l2_disagreement`.
- Analysis 1:
  - alpha distribution by label/language.
- Analysis 2:
  - alpha trajectory around code-switch boundaries.
  - window size: `3`.
  - offsets: `-3` to `+3`.
- Analysis 3:
  - alpha distribution by sentence CMI buckets.
  - CMI definition: `(N - max_lang_count) / N`.
  - Only LID uses meaningful CMI.
  - `ot` labels excluded from CMI token counts.
- Analysis 4:
  - expert L2 disagreement versus alpha.
  - correlation computed with `np.corrcoef`.
  - scatter plot capped at `5000` sampled points.
- Analysis 5:
  - cross-task alpha comparison.
  - violin plot across tasks.
- Saved outputs:
  - raw records JSON.
  - stats JSON.
  - PNG figures.

## 36. R1 LID Interpretability Stats

- File: `05_results/figures/R1_lid_stats.json`.
- Generated at: `2026-04-19T12:13:43.549530Z`.
- Number of word-level records: `18437`.
- Mean alpha: `0.42890`.
- Alpha std: `0.40741`.
- Alpha by label:
  - `en`: mean `0.84271`, std `0.19748`, count `4184`.
  - `hi`: mean `0.10972`, std `0.17630`, count `9950`.
  - `ot`: mean `0.76458`, std `0.29439`, count `4303`.
- L2 correlation with alpha: `0.22563`.
- Switch trajectory mean alpha:
  - offset `-3`: `0.40598`.
  - offset `-2`: `0.42447`.
  - offset `-1`: `0.52346`.
  - offset `0`: `0.47970`.
  - offset `1`: `0.36543`.
  - offset `2`: `0.37629`.
  - offset `3`: `0.39497`.
- CMI bucket trend:
  - `0.10-0.20`: mean alpha `0.36762`, count `4372`.
  - `0.20-0.30`: mean alpha `0.39755`, count `6789`.
  - `0.30-0.40`: mean alpha `0.48031`, count `4795`.
  - `0.40-0.50`: mean alpha `0.52331`, count `2481`.
- Important note:
  - Code says alpha near `1` means more HingBERT.
  - R1 stats show higher alpha for `en` than `hi`.
  - The report text says R1 Hindi tokens receive higher alpha.
  - The saved R1 stats contradict that textual interpretation.

## 37. R9 LID Interpretability Stats

- File: `05_results/figures/R9_lid_stats.json`.
- Generated at: `2026-04-19T12:18:55.905085Z`.
- Number of word-level records: `18437`.
- Mean alpha: `0.60935`.
- Alpha std: `0.39328`.
- Alpha by label:
  - `en`: mean `0.21078`, std `0.21139`, count `4184`.
  - `hi`: mean `0.91652`, std `0.13740`, count `9950`.
  - `ot`: mean `0.28662`, std `0.31431`, count `4303`.
- L2 correlation with alpha: `-0.26168`.
- R9 stats align with expected alpha interpretation:
  - Hindi receives high HingBERT weight.
  - English receives low HingBERT weight.
  - Other is closer to English/RoBERTa side.

## 38. R1 POS Interpretability Stats

- File: `05_results/figures/R1_pos_stats.json`.
- Generated at: `2026-04-19T12:14:25.740296Z`.
- Number of word-level records: `6669`.
- Mean alpha: `0.85391`.
- Alpha std: `0.30807`.
- L2 correlation with alpha: `-0.10766`.
- POS alpha by label:
  - `ADJ`: mean `0.42870`, count `356`.
  - `ADP`: mean `0.96978`, count `625`.
  - `ADV`: mean `0.72807`, count `213`.
  - `CONJ`: mean `0.91625`, count `166`.
  - `DET`: mean `0.38062`, count `231`.
  - `NOUN`: mean `0.94973`, count `1059`.
  - `NUM`: mean `0.35959`, count `94`.
  - `PART`: mean `0.93020`, count `264`.
  - `PART_NEG`: mean `0.88428`, count `101`.
  - `PRON`: mean `0.11866`, count `293`.
  - `PRON_WH`: mean `0.17304`, count `84`.
  - `PROPN`: mean `0.95798`, count `529`.
  - `VERB`: mean `0.96864`, count `1197`.
  - `X`: mean `0.99584`, count `1457`.
- POS switch trajectory is empty because switch-point logic only checks `hi`/`en`.
- POS CMI buckets are empty because CMI is defined only for LID.

## 39. Cross-Task Alpha Stats

- File: `05_results/figures/cross_task_stats.json`.
- POS:
  - mean alpha `0.85391`.
  - std `0.30807`.
  - count `6669`.
- LID:
  - mean alpha `0.42890`.
  - std `0.40741`.
  - count `18437`.
- Interpretation:
  - R1 routes very differently by task.
  - POS heavily favors HingBERT side on average.
  - LID has broader and lower alpha distribution.

## 40. API and Website

- Backend file: `03_code/api.py`.
- Framework: Flask.
- CORS enabled.
- Website directory: `03_code/website`.
- Main page served at `/`.
- Main endpoints:
  - `GET /`: serve analyzer UI.
  - `POST /analyze`: run inference on user text.
  - `GET /checkpoints`: list checkpoint files.
  - `GET /metrics`: return metrics.
  - `GET /figures/<filename>`: serve PNG figures.
  - `GET /analysis`: list available analysis stats.
  - `GET /analysis/stats?exp=R1&task=lid`: return specific stats JSON.
- API uses lazy tokenizer/model cache.
- Device:
  - `cuda` if available.
  - otherwise `cpu`.
- Input text is split by whitespace.
- The API builds the same dual-tokenized batch format as training.
- API can use:
  - explicit checkpoint.
  - single best checkpoint for task.
  - ensemble mode.

## 41. Ensemble Inference Logic

- Ensemble mode is in `api.py`.
- Checkpoints are scored by saved test metric.
- Chance level:
  - `1 / num_labels`.
- Normalized metric:
  - `(test_metric - chance) / (1 - chance)`.
- Weighting:
  - `softmax(norm_metric * 10)`.
- Candidate selection:
  - choose candidates within `5%` relative of best normalized metric.
  - pass 1 guarantees router architecture diversity among MoE models.
  - pass 2 fills remaining slots by experiment ID diversity.
- For POS:
  - B1 is much better than MoE.
  - threshold excludes weaker MoE checkpoints from dragging down POS.
- API response per token includes:
  - `word`.
  - `prediction`.
  - `confidence`.
  - `alpha`.
  - `l2_disagreement`.
  - `all_probs`.

## 42. CLI Usage

- Entrypoint: `03_code/main.py`.
- Modes:
  - `train`.
  - `eval`.
  - `analysis`.
  - `sweep`.
- Train example:
  - `python 03_code/main.py --task lid --mode train --exp_id R1 --seed 42`.
- Eval example:
  - `python 03_code/main.py --mode eval --checkpoint 05_results/checkpoints/R1-lid-s42.pt`.
- Full sweep:
  - `python 03_code/main.py --mode sweep`.
- API:
  - `python 03_code/api.py`.

## 43. Test Coverage

- Test file: `03_code/tests/test_alignment.py`.
- Number of tests described: `10`.
- Test focus:
  - special tokens have `word_id=None`.
  - English word counts preserved.
  - romanized Hindi word counts preserved.
  - code-mixed word counts preserved.
  - single-word input.
  - subtoken averaging correctness.
  - zero padding for absent word positions.
  - ignoring `None` positions.
  - truncation consistency.
  - word boundary preservation with `is_split_into_words=True`.
- Tests require local tokenizers/models.
- First run may download HingBERT and RoBERTa if not already local.

## 45. Prior Work Basis

- Shazeer et al. 2017:
  - Sparsely-gated Mixture of Experts.
  - Inspired routing/gating concept.
- HingBERT / Lal et al.:
  - Hindi-English code-mixed pretrained model.
  - Used as specialist expert.
- RoBERTa / Liu et al.:
  - Robustly optimized BERT pretraining.
  - Used as English generalist.
- BERT / Devlin et al.:
  - Base architecture and tokenization context.
- LinCE / Aguilar et al.:
  - Code-switching benchmark and metrics.
- Straight-through estimator / Bengio et al.:
  - Basis for hard routing in R7.

## 46. Presentation Deck Details

- File reviewed: `DL_MoE_Presentation 1.pdf`.
- PDF metadata:
  - 12 pages.
  - Created: `2026-04-27 03:33:16 IST`.
  - File size: `803932` bytes.
- Slide title:
  - `Frozen-Expert Mixture of Experts for Code-Mixed Language Modeling in Indian Social Media`.
- Slide tagline:
  - `Training only ~200K parameters to match 85M-parameter fine-tuning`.
- Course/group:
  - `CS F425 · Deep Learning · BITS Pilani`.
  - Group `14`.
- Team listed:
  - Priyanshu Narayan, `f20230283`.
  - Samarth Agrawal, `f20230761`.
  - Satyasheel Singh, `f20230261`.
  - Vaishnav Pasarge, `f20230631`.
- Presentation motivation points:
  - Hindi-English code-mixing is common in Indian social media.
  - Data scarcity: few large annotated code-mixed corpora.
  - Many corpora are noisy Twitter scrapes.
  - Romanization is inconsistent.
  - Vocabulary-based approaches break on spelling variants.
  - A model must handle Hindi morphology and English semantics simultaneously.
  - Per-token language identity is required.
- Presentation framing of fine-tuning problem:
  - Updates about `85M` parameters.
  - Expensive in GPU memory and time.
  - Risks catastrophic forgetting.
  - Forces one monolithic set of weights to handle both languages.
- Presentation description of FE-MoE:
  - Keep two complementary frozen experts.
  - Train only a lightweight router.
  - Router learns per-token trust in each expert.
  - Claimed parameter reduction: `425x`.
  - Claimed trainable fraction: `0.24%` of B1.
  - No direct alpha supervision.
- Presentation experiment count:
  - 4 baselines.
  - 9 MoE router configs.
  - 2 ablations.
  - 15 configurations across 2 seeds.
- Presentation core result:
  - R2 LID F1: `88.98%`.
  - B1 LID F1: `89.65%`.
  - Gap: `0.67` points.
  - POS gap: MoE around `67%` vs B1 `91.09%`.
- Presentation future work:
  - Partially unfreeze top 2 expert layers.
  - Extend to NER.
  - Multilingual expert triplets.
  - Sparse top-k routing.

## 47. Claimed Contributions

- Frozen dual-expert MoE for Hindi-English code-mixed sequence labeling.
- Two complementary frozen experts:
  - HingBERT.
  - RoBERTa-base.
- Lightweight router and task head trained.
- Router architectures compared:
  - MLP.
  - BiGRU.
  - CNN.
- Temperature sweep:
  - `tau` in `{0.1, 0.3, 0.5, 1.0, 2.0}`.
- Routing ablations:
  - token-level.
  - sentence-level.
  - hard routing.
  - router input both/hing/rob.
- Interpretability toolkit:
  - alpha by language.
  - switch-point trajectories.
  - CMI buckets.
  - expert disagreement correlation.
  - cross-task comparison.
- Metric-normalized ensemble inference in API.

## 48. What Worked

- Full fine-tuning works best overall.
- Frozen MoE works well for LID.
- Sharp MLP routing with `tau=0.1` gives best LID MoE performance.
- CNN router is strong for LID.
- Token-level routing is necessary for code-mixed LID.
- Learned routing beats fixed averaging on LID.
- Router alpha values can show meaningful language/task structure.

## 49. What Did Not Work

- Frozen MoE does not solve POS.
- POS accuracy remains around `64%` to `68%` for MoE variants.
- Full fine-tuning reaches about `91%` POS accuracy.
- Frozen NER models produce near-zero entity F1.
- Sentence-level routing underperforms for LID.
- Hard routing hurts POS.
- BiGRU router does not outperform CNN/MLP despite sequence capacity.
- Full concatenated router input R1 is unstable across LID seeds.

## 50. Likely Viva Questions and Short Answers

### What is the model?

- A frozen dual-expert MoE for token classification.
- It combines frozen HingBERT and frozen RoBERTa word-level embeddings using a learned scalar gate per token.

### What is alpha?

- Alpha is the router output for each token.
- `alpha=1` means use HingBERT fully.
- `alpha=0` means use RoBERTa fully.
- Blended vector is `alpha*h_hing + (1-alpha)*h_rob`.

### Why use two experts?

- HingBERT captures Hindi-English code-mixed social media patterns.
- RoBERTa captures strong English representations.
- Code-mixed text contains both Hindi and English signals.

### Why freeze experts?

- Reduces trainable parameters.
- Preserves pretrained knowledge.
- Tests whether routing alone can exploit complementary representations.

### Why is freezing hard?

- Experts cannot adapt.
- Tokenizers differ.
- Hidden spaces are not jointly aligned.
- Router gets only downstream task loss.

### How are different tokenizations handled?

- Both tokenizers are run on the same pre-split word list.
- Each tokenizer returns word ids.
- Subtoken hidden states for the same word are averaged.
- This produces one 768-dim vector per original word per expert.

### Which experiment is best for LID?

- `R2`.
- MLP router.
- `tau=0.1`.
- Weighted F1 `0.8898 +/- 0.0065`.

### Which experiment is best overall?

- `B1` full HingBERT fine-tuning.
- LID `0.8965`.
- POS `0.9109`.

### Why is R2 good?

- Low temperature sharpens routing.
- LID is close to a language-selection problem.
- Near-binary expert selection helps.

### Why does POS fail for frozen MoE?

- POS requires fine-grained syntactic geometry.
- Frozen MLM representations are not necessarily organized for POS labels.
- Router can only mix vectors, not reshape expert internals.

### Why does sentence-level routing fail?

- Code-mixed sentences switch language inside the sentence.
- One alpha for the whole sentence cannot handle token-level language changes.

### Why does CNN perform well?

- Code switches are local.
- A 5-token window captures nearby language context.
- CNN gives local context without recurrent propagation.

### What does hard routing do?

- Forward pass uses binary alpha.
- Backward pass uses soft gradient via straight-through estimator.
- It forces each token to choose one expert.

### What metrics are used?

- LID: weighted F1.
- POS: token accuracy.
- NER: seqeval entity F1.

### What is CMI?

- Code-Mixing Index.
- In code: `(N - max_lang_count) / N` over non-`ot` LID tokens.
- Higher CMI means more balanced language mixing.

### What is expert disagreement?

- L2 distance between aligned HingBERT and RoBERTa hidden vectors.
- Formula: `||h_hing - h_rob||_2`.
- Used to analyze whether routing changes when experts disagree.

### Why only train router and head?

- To make adaptation parameter-efficient.
- To test if pretrained experts already contain useful language-discriminative structure.

### What is the biggest limitation?

- POS and NER need expert adaptation.
- Frozen routing alone cannot learn all syntactic/entity-boundary structure.

## 51. Possible Improvement Directions

- Partially unfreeze top transformer layers.
- Specifically suggested in presentation: unfreeze top 2 expert layers.
- Use a learned projection to align HingBERT and RoBERTa spaces before routing.
- Add supervised auxiliary language routing loss for LID-aware alpha.
- Add CRF layer for NER or POS sequence consistency.
- Use better calibrated router regularization.
- Add load-balancing or entropy regularization for alpha.
- Try token-level adapters inside frozen experts.
- Use multilingual/code-mixed RoBERTa-like expert instead of plain RoBERTa.
- Try multilingual expert triplets.
- Try sparse top-k routing.
- Use larger and cleaner POS/NER code-mixed corpora.
- Reconcile README/report/stats inconsistencies before submission.

## 52. Known Inconsistencies to Mention Carefully

- Top-level README result table differs from saved CSV and report.
- R1 LID alpha interpretation differs between report text and saved `R1_lid_stats.json`.
- Report says router is about `200K` parameters, but full MLP both-expert router has about `394K` router parameters in code.
- Report/README prose for GRU hidden size differs from code.
- Dataset-specific inconsistencies are moved to `DATASET_DETAILS.md`.

## 53. One-Sentence Summary for Viva

- This project builds a frozen dual-expert MoE for Hinglish sequence labeling, aligns HingBERT and RoBERTa subtoken outputs to word level, learns a small token-level router to blend them, nearly matches full HingBERT fine-tuning on LID, and shows that frozen routing is insufficient for POS and NER without adapting expert representations.
