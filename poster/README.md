# Subword Fragmentation and Entity-Boundary Errors in Persian NER

Comparing **ParsBERT**, **TookaBERT-Base**, and **XLM-R-base** on ArmanPersoNER (Fold 1).

## Research question

How do the three models differ in subword fragmentation and entity-boundary accuracy on Persian NER, and is greater fragmentation associated with more boundary errors, particularly for person (PER) entities?

- **H1:** Persian-specific tokenizers produce fewer subword pieces per entity word than XLM-R's tokenizer. **Supported.**
- **H2:** More fragmented entity mentions have lower boundary accuracy, including within PER. **Not supported at the mention level.**

## Repository contents

| File | Description |
|---|---|
| `poster.ipynb` | Full pipeline: data preparation, training, evaluation, fragmentation analysis |
| `results/` | CSV outputs from the run (add your exported files here) |
| `poster.tex` | Overleaf/LaTeX source of the poster |
| `H1_panel.png`, `H2_panel.png` | Poster figures |

## Setup

- Dataset: ArmanPersoNER, Fold 1 (from [HaniehP/PersianNER](https://github.com/HaniehP/PersianNER)). **Not included in this repo**; download it from the original source.
- Models: `xlm-roberta-base`, `HooshvareLab/bert-fa-base-uncased`, `PartAI/TookaBERT-Base`.
- Training (same for all): lr 2e-5, 4 epochs, effective batch size 16, weight decay 0.01, AdamW, linear schedule, seed 42, single T4 GPU. Best epoch by validation typed micro-F1.
- Split: 10% of train held out for validation (seed 42), cross-split duplicates removed (11 sentences). Final: 4,598 train / 512 validation / 2,560 test sentences.

## Key results (test set)

**Tokenizer fragmentation**

| Model | Subwords per word | Mean SFR (PER) | PER mentions split |
|---|---|---|---|
| XLM-R | 1.289 | 2.081 | 85.7% |
| ParsBERT | 1.023 | 1.161 | 19.6% |
| TookaBERT | 1.103* | 1.329 | 32.1% |

\*Excludes 320 words (0.39%) for which the tokenizer produced no token.

**Performance (%)**

| Model | Typed micro-F1 | Macro-F1 | Boundary F1 |
|---|---|---|---|
| XLM-R | 79.76 | 71.31 | 84.96 |
| ParsBERT | 80.76 | 73.38 | 85.58 |
| TookaBERT | 81.11 | 72.85 | 86.06 |

**PER boundary accuracy**

| Model | Boundary recall | Boundary error |
|---|---|---|
| XLM-R | 93.96% | 6.04% |
| ParsBERT | 96.31% | 3.69% |
| TookaBERT | 95.05% | 4.95% |

**Fragmented vs. unfragmented PER mentions (boundary error, adjusted difference)**

| Model | Unfragmented | Fragmented | Adjusted diff. (pp) |
|---|---|---|---|
| XLM-R | 15.72% (n=159) | 4.42% (n=951) | -15.37 |
| ParsBERT | 3.81% (n=893) | 3.23% (n=217) | -0.95 |
| TookaBERT | 4.77% (n=754) | 5.34% (n=356) | -2.18 |

Negative = fragmented mentions had fewer errors.

## Conclusion

Persian-specific tokenizers fragment Persian entity words much less than XLM-R (H1). Across models, PER boundary error follows fragmentation, but the models differ in far more than tokenization, so this is not causal. Within models, fragmented PER mentions are not more error-prone (H2 not supported).

## Limitations

One fold and one training seed per model; only gold-mention boundary recall is analysed (no false positives); small groups (e.g. 159 unfragmented XLM-R mentions); stratified comparisons are descriptive without confidence intervals.

