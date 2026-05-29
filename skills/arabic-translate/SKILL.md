---
name: arabic-translate
description: >-
  Translates English benchmark items into Modern Standard Arabic (MSA) while
  preserving gold labels and task semantics. Applies XNLI-style context
  isolation (translate linked fields separately) and mandatory label-drift
  spot checks. Use when translating evaluation datasets, MCQ/NLI/QA items,
  or any labeled benchmark content into Arabic.
disable-model-invocation: true
---

# Arabic Benchmark Translation

Translate labeled benchmark items from English to **Modern Standard Arabic (MSA)**.
Follow the methodology of Conneau et al. (2018, XNLI, arXiv:1809.05053): isolate
context during translation, copy labels from source, then verify labels did not drift.

## Core principles (from XNLI)

### 1. Context isolation — translate linked fields separately

**Problem:** If a translator sees the full item (stem + options, premise + hypothesis,
passage + question), they unconsciously "repair" later fields so the pair reads
naturally or matches the intended relation. That injects annotation leakage and
changes what the benchmark measures.

**Rule:** Never translate an entire labeled instance in one pass when fields are
logically paired.

| Benchmark shape | Translate separately |
|-----------------|---------------------|
| NLI | premise, then hypothesis (each without the other) |
| MCQ | question stem, then each option A/B/C/D independently |
| Reading comprehension | passage, then question, then each option |
| True/false | statement only; do not peek at the boolean label |
| Fill-in-the-blank | sentence with blank masked or replaced by `[___]` first; translate options separately |

**Workflow per item:**

1. List all translatable fields and their isolation groups.
2. Translate each field alone; do not show sibling fields or the gold label.
3. Assemble the Arabic item only after all fields are done.
4. Copy `answer`, `label`, or boolean gold **verbatim from English** — do not re-derive from Arabic text.

### 2. Label drift — assume labels can break; spot-check is mandatory

**Problem:** Translation can add/remove negation, shift modality, or change entity
relations so the **gold label no longer holds** in Arabic. XNLI found rare but real
cases (e.g. *upright* entailment → Chinese *sitting upright* contradiction). Labels
copied from English are an **assumption**, not a guarantee.

**Rule:** After bulk translation, run a structured label audit before shipping.

**Minimum audit (adapt size to corpus):**

- Random sample **≥100 items** (or 10–15% of dev set, whichever is larger).
- Bilingual reviewer re-labels from **Arabic only** — no English source visible.
- Compare recovered label vs copied gold; track agreement rate.
- XNLI baseline: ~83–85% agreement is acceptable for human translation; **investigate all mismatches**.
- Prioritize audit on: negation, comparatives, quantifiers, temporal/aspect, entailment/contradiction pairs, and domain polysemy.

**Mismatch handling:**

| Severity | Action |
|----------|--------|
| Wording preference only | Keep translation; document |
| Label no longer defensible | Fix translation **or** update gold with documented rationale |
| Systematic pattern (e.g. negation drops) | Stop batch; fix guideline; re-translate affected slice |

## Arabic-specific guidelines

- **Register:** MSA for benchmark text; avoid dialect unless the source explicitly requires it.
- **Script:** Arabic script only for body text; keep JSON keys, option letters (`A`–`D`), indices, and enum labels in source schema.
- **RTL:** Preserve logical field order in data files; do not reorder options for readability.
- **Technical terms:** Prefer established Arabic equivalents in Earth/remote-sensing/geoscience literature; keep unavoidable Latin abbreviations (e.g. SMAP, DEM, LiDAR) unchanged.
- **Negation:** Explicitly preserve `no`, `not`, `never`, `without` — top cue for label flip in XNLI-style data.
- **Numbers/units:** Keep SI units; localize number formatting only if the downstream evaluator expects it.

## Translation pass checklist

Copy and track:

```
Arabic translation progress:
- [ ] Field isolation map written for this dataset schema
- [ ] Bulk translate: stems / passages / hypotheses (no labels, no paired fields)
- [ ] Bulk translate: each option / hypothesis variant in isolation
- [ ] Assemble JSON/JSONL; gold labels copied unchanged from English
- [ ] Spot-check sample drawn (record seed + item ids)
- [ ] Bilingual re-label from Arabic-only view complete
- [ ] Agreement rate recorded; mismatches triaged
- [ ] Final file: UTF-8, valid JSONL, same keys and idx as source
```

## Output format

- One output row per source row; same `idx`, `answer`/`label`, metadata keys.
- Translate human-readable string fields only unless user specifies otherwise.
- Do **not** translate `reasoning_chain` or other fields marked eval-only unless explicitly requested.

## When automating with an LLM

LLM translators exhibit the same "repair" bias as human translators. Mitigations:

1. One API call per isolated field; system prompt forbids seeing other fields or the label.
2. Second pass: Arabic-only label verification on the sample (different prompt, no English).
3. Never ask the model to "pick the correct option" during translation.

## References

- Conneau et al. (2018). *XNLI: Evaluating Cross-lingual Sentence Representations.* arXiv:1809.05053 — separate premise/hypothesis translation; copy labels; bilingual re-annotation on 100 dev examples.
