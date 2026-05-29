---
name: arabic-translate
description: >-
  Translates benchmark items from a source language (English, Chinese, or other)
  into Modern Standard Arabic (MSA) while preserving gold labels and task
  semantics. Applies XNLI-style context isolation (translate linked fields
  separately) and mandatory label-drift spot checks. Use when translating
  evaluation datasets, MCQ/NLI/QA items, or any labeled benchmark content into
  Arabic from any source language.
disable-model-invocation: true
---

# Arabic Benchmark Translation

Translate labeled benchmark items **into Modern Standard Arabic (MSA)** from a
**source language** specified by the user (commonly English or Chinese).

Follow the methodology of Conneau et al. (2018, XNLI, arXiv:1809.05053): isolate
context during translation, copy labels from source, then verify labels did not drift.

## Before starting

1. **Confirm source language** with the user if not stated (e.g. `en`, `zh`).
2. Record source language in output metadata or filename (e.g. `Earth-Silver__mc.zh2ar.jsonl`).
3. Apply the same isolation and label-audit rules regardless of source language.

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
2. Translate each field alone from the source language; do not show sibling fields or the gold label.
3. Assemble the Arabic item only after all fields are done.
4. Copy `answer`, `label`, or boolean gold **verbatim from the source dataset** — do not re-derive from Arabic text.

### 2. Label drift — assume labels can break; spot-check is mandatory

**Problem:** Translation can add/remove negation, shift modality, or change entity
relations so the **gold label no longer holds** in Arabic. XNLI found rare but real
cases (e.g. English *upright* entailment → Chinese *sitting upright* → contradiction).
Labels copied from the source are an **assumption**, not a guarantee.

**Rule:** After bulk translation, run a structured label audit before shipping.

**Minimum audit (adapt size to corpus):**

- Random sample **≥100 items** (or 10–15% of dev set, whichever is larger).
- Bilingual reviewer (Arabic + source language) re-labels from **Arabic only** — no source text visible.
- Compare recovered label vs copied gold; track agreement rate.
- XNLI baseline: ~83–85% agreement is acceptable for human translation; **investigate all mismatches**.
- Prioritize audit on: negation, comparatives, quantifiers, temporal/aspect, entailment/contradiction pairs, and domain polysemy.

**Mismatch handling:**

| Severity | Action |
|----------|--------|
| Wording preference only | Keep translation; document |
| Label no longer defensible | Fix translation **or** update gold with documented rationale |
| Systematic pattern (e.g. negation drops) | Stop batch; fix guideline; re-translate affected slice |

## Source-language notes

Apply the section that matches the confirmed source language.

### From English (`en`)

- Preserve explicit negation: `no`, `not`, `never`, `without`.
- Keep Latin abbreviations and proper nouns as in source unless a standard Arabic equivalent exists.

### From Chinese (`zh`)

- Use **Modern Standard Arabic**, not dialect; source may be simplified or traditional — confirm with user.
- **Negation:** Map 不/没/未/无/非 explicitly; do not soften or drop double negatives.
- **Polysemy:** Chinese compact phrasing often hides modality or scope — verify entailment/contradiction pairs extra carefully in audit.
- **Proper nouns & terms:** Transliterate or use established Arabic equivalents for place names and geoscience terms; keep Latin abbreviations (SMAP, DEM, LiDAR) unchanged.
- **Numbers/units:** Preserve numeric values and SI units; do not convert 万/亿 unless the evaluator expects it.

### From other languages

- Mirror the same isolation and audit rules.
- Document source-specific negation markers and common drift patterns before bulk translation.

## Arabic target guidelines

- **Register:** MSA for benchmark text; avoid dialect unless the source explicitly requires it.
- **Script:** Arabic script only for body text; keep JSON keys, option letters (`A`–`D`), indices, and enum labels in source schema.
- **RTL:** Preserve logical field order in data files; do not reorder options for readability.
- **Technical terms:** Prefer established Arabic equivalents in Earth/remote-sensing/geoscience literature; keep unavoidable Latin abbreviations unchanged.
- **Numbers/units:** Keep SI units; localize number formatting only if the downstream evaluator expects it.

## Translation pass checklist

Copy and track:

```
Arabic translation progress:
- [ ] Source language confirmed (en / zh / other)
- [ ] Field isolation map written for this dataset schema
- [ ] Bulk translate: stems / passages / hypotheses (no labels, no paired fields)
- [ ] Bulk translate: each option / hypothesis variant in isolation
- [ ] Assemble JSON/JSONL; gold labels copied unchanged from source
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

1. One API call per isolated field; system prompt states source language and forbids seeing other fields or the label.
2. Second pass: Arabic-only label verification on the sample (different prompt, no source text).
3. Never ask the model to "pick the correct option" during translation.

## References

- Conneau et al. (2018). *XNLI: Evaluating Cross-lingual Sentence Representations.* arXiv:1809.05053 — separate premise/hypothesis translation; copy labels; bilingual re-annotation on 100 dev examples.
