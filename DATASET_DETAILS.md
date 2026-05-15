# Dataset Details: Original Data, Acquisition, Cleaning, and Analysis

## 1. Scope

- This file covers all dataset-related details for the project.
- Main datasets used in reported experiments:
  - LID: COMI-LINGUA Hindi-English language identification.
  - POS: Twitter Hindi-English POS TSV dataset.
- NER dataset is implemented in code but excluded from the main sweep.
- Full raw/cleaned dataset files are not present in the current workspace.
- The code expects data under `models_and_data/`.
- `models_and_data/` was not present when this file was written.
- Details below are derived from:
  - `03_code/setup_data.py`.
  - `03_code/data.py`.
  - `04_data/data_description.md`.
  - `04_data/dataset_links.txt`.
  - `04_data/sample_inputs/`.
  - `05_results/figures/*_stats.json`.
  - `DL_MoE_Presentation 1.pdf`.

## 2. How We Obtained the Original Data

### Acquisition Script

- Main acquisition script: `03_code/setup_data.py`.
- Purpose:
  - download pretrained models.
  - download HuggingFace datasets.
  - download raw GitHub POS/NER files.
  - save everything under `models_and_data/`.
- Script creates:
  - `models_and_data/comi_lingua/`.
- Script skips files/directories that already exist.
- Script is intended as one-time setup for offline use.

### Downloaded Pretrained Models

- HingBERT:
  - HuggingFace ID: `l3cube-pune/hing-bert`.
  - Local save path: `models_and_data/hing-bert`.
  - Download code:
    - `AutoTokenizer.from_pretrained(hf_name).save_pretrained(local_path)`.
    - `AutoModel.from_pretrained(hf_name).save_pretrained(local_path)`.
- RoBERTa-base:
  - HuggingFace ID: `roberta-base`.
  - Local save path: `models_and_data/roberta-base`.
  - Downloaded the same way as HingBERT.

### Downloaded COMI-LINGUA Data

- Source: HuggingFace Hub.
- Dataset ID: `LingoIITGN/COMI-LINGUA`.
- Setup script downloads three task subsets:
  - `LID`.
  - `POS`.
  - `NER`.
- Download code:

```python
ds = load_dataset("LingoIITGN/COMI-LINGUA", task)
ds.save_to_disk(path)
```

- Saved paths:
  - `models_and_data/comi_lingua/LID`.
  - `models_and_data/comi_lingua/POS`.
  - `models_and_data/comi_lingua/NER`.
- Main reported LID experiments use the COMI-LINGUA LID path.
- Important nuance:
  - although COMI-LINGUA POS and NER are downloaded by setup script, the current POS/NER loaders in `03_code/data.py` use separate GitHub files instead.

### Downloaded POS Data

- Source used by current setup script: GitHub raw TSV.
- URL:

```text
https://raw.githubusercontent.com/soicalnlpataclAnon/A-Twitter-Hindi-English-Code-Mixed-Dataset-for-POS-Tagging/master/finalData.tsv
```

- Local save path:

```text
models_and_data/finalData.tsv
```

- `04_data/dataset_links.txt` also lists a related POS URL:

```text
https://raw.githubusercontent.com/SilentFlame/hindi-pos/master/data/hindi_pos_tagged.txt
```

- Current code does not read that SilentFlame POS URL directly.
- Current code reads only `models_and_data/finalData.tsv`.

### Downloaded NER Data

- Source used by current setup script: GitHub raw CSV.
- URL:

```text
https://raw.githubusercontent.com/SilentFlame/Named-Entity-Recognition/master/Twitterdata/annotatedData.csv
```

- Local save path:

```text
models_and_data/annotatedData.csv
```

- `04_data/dataset_links.txt` also lists a related NER URL:

```text
https://raw.githubusercontent.com/SilentFlame/hindi-ner/master/data/ner_dataset.csv
```

- Current code does not read that `ner_dataset.csv` URL directly.
- Current code reads only `models_and_data/annotatedData.csv`.

## 3. Original Dataset Summary

### LID Original Dataset

- Original dataset: COMI-LINGUA.
- Source: HuggingFace Hub.
- HuggingFace ID: `LingoIITGN/COMI-LINGUA`.
- Task subset used: `LID`.
- Text type: Hindi-English code-mixed social media text.
- Annotation type: token-level language labels.
- Labels configured in project:
  - `hi`: Hindi.
  - `en`: English.
  - `ot`: Other.
- Metric used by project:
  - weighted F1.
- Presentation description:
  - expert-annotated social-media text.
  - high inter-annotator agreement, `kappa >= 0.81`.
  - romanized Hindi-English data emphasized.

### POS Original Dataset

- Original dataset used by current code: Twitter Hindi-English code-mixed POS TSV.
- Source: GitHub raw file from `soicalnlpataclAnon/A-Twitter-Hindi-English-Code-Mixed-Dataset-for-POS-Tagging`.
- Local filename: `finalData.tsv`.
- Expected token-line format:

```text
word<TAB>lang<TAB>pos
```

- The loader uses:
  - first column as token/word.
  - second column is language but not used for modeling.
  - third column as POS label.
- Verified from the downloaded local file:
  - non-separator token rows have 3 tab-separated fields.
  - language column values include `en`, `hi`, and `rest`.
  - separator rows appear as empty/tab-only lines in the raw file.
  - after Python `line.strip()`, separator rows are treated as sentence boundaries.
- Project POS labels:
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
- Metric used by project:
  - token-level accuracy.

### NER Original Dataset

- Original NER dataset used by current code: SilentFlame Hindi-English NER CSV.
- Source: GitHub raw file from `SilentFlame/Named-Entity-Recognition`.
- Local filename: `annotatedData.csv`.
- Expected CSV columns:
  - `Sent`.
  - `Word`.
  - `Tag`.
- Raw tag examples handled by mapping:
  - `Other`.
  - `B-Per`.
  - `I-Per`.
  - `B-Org`.
  - `I-Org`.
  - `B-Loc`.
  - `I-Loc`.
- Project-normalized labels:
  - `O`.
  - `B-PERSON`.
  - `I-PERSON`.
  - `B-ORGANIZATION`.
  - `I-ORGANIZATION`.
  - `B-LOCATION`.
  - `I-LOCATION`.
- Metric used by project:
  - entity-level F1 using `seqeval`.
- NER status:
  - implemented.
  - excluded from main sweep.
  - frozen models produced near-zero entity F1.

## 4. What the Raw Data Looked Like

### COMI-LINGUA Raw Structure

- COMI-LINGUA is loaded as a HuggingFace `DatasetDict` saved on disk.
- Loader expects splits:
  - `train`.
  - `test`.
- Code searches dataset columns for one containing:

```text
Annotator 1
```

- Annotation field can appear in multiple formats.
- Supported raw annotation formats:
  - a Python list object.
  - a stringified Python list parsed with `ast.literal_eval`.
  - list of dictionaries with fields:
    - `key`: token.
    - `value`: label.
  - list of dictionaries with fields:
    - `word`: token.
    - `entity`: label.
  - list/tuple pairs:
    - `[word, label]`.
- Conceptual raw LID example:

```python
[
  {"key": "yaar", "value": "hi"},
  {"key": "presentation", "value": "en"},
  {"key": "ready", "value": "en"},
  {"key": "hai", "value": "hi"}
]
```

- Exact rows cannot be shown from local raw files because `models_and_data/` is absent.

### POS Raw Structure

- Raw POS data is expected as a plain TSV file.
- Each token line has exactly 3 tab-separated fields.
- Sentence boundaries are represented by blank or malformed separator lines.
- In the downloaded file, sentence boundaries are tab-only/empty-looking separator lines.
- The loader strips each line before splitting, so those separator lines become non-3-field lines and close the current sentence.
- Conceptual raw POS example:

```text
yaar	hi	NOUN
kal	hi	NOUN
ka	hi	ADP
presentation	en	NOUN
ready	en	ADJ
hai	hi	VERB
kya	hi	PART

```

- The second column (`lang`) is read but not used.
- The third column (`pos`) becomes the supervised label.

### NER Raw Structure

- Raw NER data is expected as CSV.
- Relevant columns:
  - `Sent`: sentence ID.
  - `Word`: token.
  - `Tag`: raw entity tag.
- Tokens are grouped by the `Sent` id.
- Conceptual raw NER example:

```csv
Sent,Word,Tag
1,Delhi,B-Loc
1,mein,Other
1,Google,B-Org
1,office,Other
```

## 5. Cleaning Pipeline Overview

- Cleaning happens in `03_code/data.py`.
- Main functions:
  - `load_comi_lingua(task)`.
  - `load_pos_data()`.
  - `load_ner_data()`.
  - `make_val_split(...)`.
  - `_tokenize_sample(...)`.
  - `CodeMixedDataset`.
- There is no separate saved cleaned dataset file in the repo.
- Cleaned samples are created in memory as:

```python
(words: list[str], labels: list[str])
```

- Tokenized cleaned samples are stored in memory as dictionaries with:
  - token IDs for HingBERT.
  - attention mask for HingBERT.
  - word IDs for HingBERT.
  - token IDs for RoBERTa.
  - attention mask for RoBERTa.
  - word IDs for RoBERTa.
  - word-level label IDs.
  - word count.
  - original words after truncation.

## 6. COMI-LINGUA Cleaning

### Loader

- Function: `load_comi_lingua(task)`.
- Input task examples:
  - `lid`.
  - `ner`.
  - `pos` if called.
- Path:

```text
models_and_data/comi_lingua/{task.upper()}
```

- Loading method:

```python
ds = load_from_disk(f"models_and_data/comi_lingua/{task.upper()}")
```

### Annotation Column Selection

- Code reads column names from:

```python
ds["train"].column_names
```

- Annotation column selected as first column whose name includes:

```text
Annotator 1
```

- If no such column exists:
  - raises `ValueError`.

### Annotation Parsing

- Helper: `_parse_annotation(ann)`.
- If `ann` is a list:
  - used directly.
- If `ann` is a string:
  - parsed using `ast.literal_eval`.
- If `ann` is neither list nor string:
  - returns `(None, None)`.
- Empty annotation list:
  - returns empty `words` and `labels`.
- Supported list item forms:
  - dictionary with `key` and `value`.
  - dictionary with `word` and `entity`.
  - list/tuple pair.
- Unsupported item form:
  - returns `(None, None)`.

### Dropped COMI-LINGUA Rows

- Dropped if annotation is `None`.
- Dropped if parsed words are `None`.
- Dropped if word list is empty.
- Dropped if number of words differs from number of labels.
- Dropped if any Devanagari character appears in the joined sentence.

### Devanagari Filtering

- Regex:

```python
DEVANAGARI_RE = re.compile(r"[\u0900-\u097F]")
```

- Check:

```python
DEVANAGARI_RE.search(" ".join(words))
```

- If matched:
  - sentence is dropped.
- Reason:
  - keep dataset purely romanized / English code-mixed.
  - project focuses on Hinglish written in Roman script.

### Unknown Label Handling

- Known labels are read from `configs.TASK_LABELS[task.lower()]`.
- Unknown labels are mapped to the first configured label.
- For LID:
  - unknown label -> `hi`.
- For POS:
  - unknown label -> `NOUN`.
- For NER:
  - unknown label -> `O`.
- This prevents crashes from unexpected labels.
- It may introduce noise if many unknown labels exist.

### NER-Specific COMI-LINGUA Handling

- If task is NER, labels pass through `_flat_ner_to_bio(labels)`.
- `_flat_ner_to_bio` behavior:
  - `X` becomes `O`.
  - first token of a type becomes `B-{type}`.
  - consecutive same type becomes `I-{type}`.
- This converts flat entity type labels into BIO sequence labels.

## 7. POS Cleaning

### Loader

- Function: `load_pos_data()`.
- File:

```text
models_and_data/finalData.tsv
```

### Parsing Rules

- Reads entire file with UTF-8 encoding.
- Splits into lines.
- For each line:
  - strips whitespace.
  - splits on `\t`.
- If line has exactly 3 fields:
  - append field 0 to `words`.
  - append field 2 to `labels`.
- If line does not have exactly 3 fields and current sentence has words:
  - close current sentence.
  - append `(words, labels)` to samples.
  - reset sentence buffers.
- After loop:
  - append final sentence if words remain.

### Cleaning Effects

- Malformed token lines are not used as tokens.
- Blank or tab-only separator lines act as sentence boundaries after `strip()`.
- The language column is ignored.
- No Devanagari filtering is applied in the POS loader.
- No explicit unknown POS label filtering happens in `load_pos_data`.
- Unknown POS labels are handled later during tokenization:
  - `label2id.get(l, 0)`.
  - unknown label maps to label id `0`.
  - POS label id `0` is `NOUN`.

### POS Split Logic

- Uses deterministic shuffle:

```python
rng = random.Random(0)
rng.shuffle(samples)
```

- Test size:

```python
n_test = max(1, int(len(samples) * 0.2))
```

- Test samples:
  - first `n_test` shuffled samples.
- Train-all samples:
  - remaining samples.
- Validation is later carved out by `make_val_split`.

## 8. NER Cleaning

### Loader

- Function: `load_ner_data()`.
- File:

```text
models_and_data/annotatedData.csv
```

### Parsing Rules

- Opens CSV with UTF-8 encoding.
- Uses `csv.DictReader`.
- For each row:
  - sentence id from `Sent`.
  - word from `Word`.
  - raw tag from `Tag`.
- Strips whitespace from all three fields.
- Drops row if sentence id is empty.
- Drops row if word is empty.
- Groups tokens by sentence id.

### Tag Normalization

- Mapping:

| Raw tag | Clean tag |
|---|---|
| `Other` | `O` |
| `B-Per` | `B-PERSON` |
| `I-Per` | `I-PERSON` |
| `B-Org` | `B-ORGANIZATION` |
| `I-Org` | `I-ORGANIZATION` |
| `B-Loc` | `B-LOCATION` |
| `I-Loc` | `I-LOCATION` |

- Unknown or missing raw tag:
  - maps to `O`.

### NER Split Logic

- Builds samples as sentence-level `(words, labels)` pairs.
- Removes empty sentences.
- Uses deterministic shuffle:

```python
rng = random.Random(0)
rng.shuffle(samples)
```

- Test size:

```python
n_test = max(1, int(len(samples) * 0.2))
```

- Test samples:
  - first `n_test` shuffled samples.
- Train-all samples:
  - remaining samples.
- Validation is later carved out by `make_val_split`.

## 9. Validation Split

- Function: `make_val_split(train_samples, val_frac=0.1, seed=42)`.
- Used for all tasks after train/test loading.
- Makes a copy of training samples.
- Shuffles copy with the experiment seed.
- Validation size:

```python
n_val = max(1, int(len(shuffled) * val_frac))
```

- Default validation fraction:
  - `0.1`.
- Returns:
  - train subset: `shuffled[n_val:]`.
  - validation subset: `shuffled[:n_val]`.
- For LID:
  - COMI-LINGUA native train/test split is used.
  - 10% of native train is validation.
- For POS:
  - custom 80/20 split is used first.
  - 10% of custom train-all is validation.
- For NER:
  - custom 80/20 split is used first.
  - 10% of custom train-all is validation.

## 10. Tokenization-Time Cleaning and Truncation

### Tokenization Function

- Function: `_tokenize_sample(words, labels, label2id, hing_tok, rob_tok, max_len)`.
- Input:
  - cleaned word list.
  - cleaned string labels.
  - label-to-id dictionary.
  - HingBERT tokenizer.
  - RoBERTa tokenizer.
  - max sequence length.

### Tokenization Settings

- Both tokenizers use:

```python
is_split_into_words=True
truncation=True
max_length=max_len
padding="max_length"
return_tensors="pt"
```

- Default max length from config:
  - `128`.
- RoBERTa tokenizer is loaded with:

```python
add_prefix_space=True
```

- Reason:
  - RoBERTa BPE needs prefix-space behavior for pre-tokenized input.

### Word Count After Truncation

- HingBERT word count:
  - max non-`None` HingBERT word id + 1.
- RoBERTa word count:
  - max non-`None` RoBERTa word id + 1.
- Final word count:

```python
num_words = min(hing_max, rob_max, len(words))
```

- Reason:
  - different tokenizers can truncate differently.
  - final count must be safe for both expert alignments.

### Label Truncation

- Labels are truncated to `num_words`.
- Label IDs:

```python
label_ids = [label2id.get(l, 0) for l in labels[:num_words]]
```

- Unknown labels at this stage become id `0`.
- For LID, id `0` is `hi`.
- For POS, id `0` is `NOUN`.
- For NER, id `0` is `O`.

### Dropping Empty Tokenized Samples

- `CodeMixedDataset` tokenizes all samples.
- Then it filters:

```python
self.data = [d for d in self.data if d["num_words"] > 0]
```

- Any sample that becomes zero words after tokenization is dropped.

## 11. Batch Preparation

- Function: `collate_fn`.
- Pads labels to max word count in current batch.
- Padding label:

```python
-100
```

- `-100` is ignored by `CrossEntropyLoss`.
- Batch contains:
  - HingBERT input IDs.
  - HingBERT attention mask.
  - HingBERT word IDs.
  - RoBERTa input IDs.
  - RoBERTa attention mask.
  - RoBERTa word IDs.
  - padded labels.
  - number of words.
  - original words.

## 12. Approximate Dataset Sizes

- Exact raw counts cannot be recomputed locally without `models_and_data/`.
- Approximate split sizes from `04_data/data_description.md`:

| Task | Train | Validation | Test |
|---|---:|---:|---:|
| LID | ~8100 | ~900 | ~2900 |
| POS | ~3200 | ~400 | ~1000 |
| NER | ~3600 | ~400 | ~1100 |

- These are approximate documented sizes.
- They should be verified by rerunning setup and loaders if exact viva numbers are needed.

## 12A. Verified POS File Statistics After Setup

- File inspected: `models_and_data/finalData.tsv`.
- Raw lines: `34499`.
- Valid token lines after `line.strip().split("\t")`: `33010`.
- Sentence boundary lines: `1489`.
- Parsed sentence count: `1489`.
- Minimum sentence length: `4` tokens.
- Maximum sentence length: `53` tokens.
- Language column counts:
  - `en`: `12589`.
  - `rest`: `10539`.
  - `hi`: `9882`.
- POS label counts:
  - `X`: `7581`.
  - `VERB`: `5984`.
  - `NOUN`: `5043`.
  - `ADP`: `2982`.
  - `PROPN`: `2737`.
  - `ADJ`: `1548`.
  - `PRON`: `1456`.
  - `PART`: `1428`.
  - `DET`: `1141`.
  - `ADV`: `1021`.
  - `CONJ`: `809`.
  - `PART_NEG`: `468`.
  - `PRON_WH`: `421`.
  - `NUM`: `391`.
- First parsed sentence words:

```text
Pak is in confused state of mind . #SurgicalStrike hui ya nahi is par contradictory statements aa rahe hain . Nahi huyi to tension mai kyon ?
```

- First parsed sentence POS labels:

```text
PROPN VERB ADP ADJ NOUN ADP VERB X X VERB CONJ PART_NEG DET ADP NOUN NOUN VERB VERB VERB X PART_NEG PART PART NOUN ADP PRON_WH X
```

## 13. Saved Analysis Counts

- Full raw data is absent, but saved model-analysis outputs include evaluated token counts.
- R1 LID analysis:
  - file: `05_results/figures/R1_lid_stats.json`.
  - word-level test records: `18437`.
- R9 LID analysis:
  - file: `05_results/figures/R9_lid_stats.json`.
  - word-level test records: `18437`.
- R1 POS analysis:
  - file: `05_results/figures/R1_pos_stats.json`.
  - word-level test records: `6669`.
- Cross-task analysis:
  - file: `05_results/figures/cross_task_stats.json`.
  - LID count: `18437`.
  - POS count: `6669`.

## 14. Label Distribution From Saved Analysis

### LID Test Label Counts

- Source: `R1_lid_stats.json`.
- These counts are for word-level records extracted during R1 LID analysis.

| Label | Count | Meaning |
|---|---:|---|
| `hi` | 9950 | Hindi |
| `en` | 4184 | English |
| `ot` | 4303 | Other |
| Total | 18437 | All analyzed test tokens |

- Approximate proportions:
  - `hi`: about `54.0%`.
  - `en`: about `22.7%`.
  - `ot`: about `23.3%`.
- Analysis:
  - Hindi tokens dominate the LID test token distribution.
  - English and Other are both substantial.
  - Weighted F1 is appropriate because class counts are imbalanced.

### POS Test Label Counts

- Source: `R1_pos_stats.json`.
- These counts are for word-level records extracted during R1 POS analysis.

| Label | Count |
|---|---:|
| `X` | 1457 |
| `VERB` | 1197 |
| `NOUN` | 1059 |
| `ADP` | 625 |
| `PROPN` | 529 |
| `ADJ` | 356 |
| `PRON` | 293 |
| `PART` | 264 |
| `DET` | 231 |
| `ADV` | 213 |
| `CONJ` | 166 |
| `PART_NEG` | 101 |
| `NUM` | 94 |
| `PRON_WH` | 84 |
| Total | 6669 |

- Analysis:
  - POS labels are strongly imbalanced.
  - `X`, `VERB`, and `NOUN` are the largest categories.
  - Rare classes include `PRON_WH`, `NUM`, and `PART_NEG`.
  - Accuracy can hide rare-class errors.
  - A macro-F1 POS analysis would reveal rare-class performance better than accuracy.

## 15. Alpha-Based Dataset/Router Analysis

### R1 LID Alpha by Label

- Source: `R1_lid_stats.json`.
- Mean alpha by label:
  - `en`: `0.84271`.
  - `hi`: `0.10972`.
  - `ot`: `0.76458`.
- Code definition:
  - alpha near `1` means more HingBERT.
  - alpha near `0` means more RoBERTa.
- Important caveat:
  - R1 saved stats show higher alpha for English than Hindi.
  - This contradicts the report/presentation textual claim for R1.
  - R9 stats align with the expected interpretation.

### R9 LID Alpha by Label

- Source: `R9_lid_stats.json`.
- Mean alpha by label:
  - `hi`: `0.91652`.
  - `en`: `0.21078`.
  - `ot`: `0.28662`.
- Analysis:
  - R9 routes Hindi tokens strongly toward HingBERT.
  - R9 routes English tokens more toward RoBERTa.
  - R9 router behavior matches intuitive expert specialization.

### CMI Analysis

- CMI means Code-Mixing Index.
- Code formula for LID:

```python
(N - max_lang_count) / N
```

- `ot` labels are excluded.
- Higher CMI means more balanced language mixing.
- R1 LID CMI bucket trend:
  - `0.10-0.20`: mean alpha `0.36762`, count `4372`.
  - `0.20-0.30`: mean alpha `0.39755`, count `6789`.
  - `0.30-0.40`: mean alpha `0.48031`, count `4795`.
  - `0.40-0.50`: mean alpha `0.52331`, count `2481`.
- Analysis:
  - More mixed sentences have higher mean alpha for R1.
  - The model's routing distribution changes with sentence-level mixing intensity.

## 16. Sample Inputs in Repo

### LID Samples

- File: `04_data/sample_inputs/lid_samples.json`.
- Samples:
  - `mujhe bahut zyada hunger lag rahi hai`.
  - `aaj maine ek bahut accha movie dekha`.
  - `I love eating golgappe with chaat masala`.
  - `yaar kal ka presentation ready hai kya`.
  - `this project is really interesting and challenging`.
- Intended patterns:
  - Hindi-dominant Hinglish.
  - English content words embedded in Hindi syntax.
  - English-dominant sentences with Hindi food terms.
  - pure English sentence.

### POS Samples

- File: `04_data/sample_inputs/pos_samples.json`.
- Samples:
  - `mujhe bahut zyada hunger lag rahi hai`.
  - expected rough tags: `PRON ADV ADV NOUN VERB VERB VERB`.
  - `aaj maine ek bahut accha movie dekha`.
  - expected rough tags: `ADV PRON DET ADV ADJ NOUN VERB`.
  - `yaar kal ka presentation ready hai kya`.
  - expected rough tags: `NOUN NOUN ADP NOUN ADJ VERB PART`.

## 17. Dataset Inconsistencies to Know

- Presentation says LID and POS use native COMI-LINGUA splits.
- Current code uses COMI-LINGUA for LID, but POS uses `models_and_data/finalData.tsv`.
- Presentation says all datasets are downloaded automatically via HuggingFace Hub.
- Current setup script downloads POS and NER from GitHub raw URLs.
- `dataset_links.txt` and `setup_data.py` list different POS/NER GitHub URLs.
- Current code is the authoritative source for what is actually loaded.
- The absence of `models_and_data/` means exact raw and cleaned counts cannot be verified from local files right now.

## 18. Short Viva Answer

- We obtained LID from the COMI-LINGUA HuggingFace dataset and POS/NER from raw GitHub files downloaded by `setup_data.py`.
- The original LID data had token-level annotations inside an `Annotator 1` column, POS was a token-per-line TSV with `word`, `language`, and `POS`, and NER was a CSV with `Sent`, `Word`, and `Tag`.
- We cleaned LID by parsing annotation formats, dropping malformed rows, dropping empty or length-mismatched samples, filtering Devanagari-script sentences, and mapping unknown labels to a default.
- We cleaned POS by reading only valid three-column token lines, using blank/malformed lines as sentence boundaries, and making deterministic 80/20 train/test splits.
- We cleaned NER by dropping rows with missing sentence IDs or words, grouping tokens by sentence, and mapping raw entity tags into standard BIO labels.
- During tokenization, we truncated labels safely to the shared word count after both HingBERT and RoBERTa tokenization, and dropped samples with zero surviving words.
