---
name: arabic-translate
description: >-
  Translates benchmark items from a source language (English, Chinese, or other)
  into Omani Arabic (Muscat official). Translates linked fields separately
  (question, options, etc.) and copies gold labels unchanged. Translates place
  names and domain nouns into Arabic, with original form in parentheses when
  helpful. Use when translating evaluation datasets into Omani Arabic.
disable-model-invocation: true
---

# Omani Arabic Benchmark Translation

Translate labeled benchmark items **into Omani Arabic (Muscat official / لهجة مسقط)**
from the user's source language (commonly English or Chinese).

## Core rule: translate fields separately

Do **not** translate a full item in one pass when fields are paired (question +
options, premise + hypothesis, passage + question, etc.). Translate each field
**alone**, without showing sibling fields or the gold label. Assemble the row
after all fields are done.

| Benchmark shape | Translate separately |
|-----------------|---------------------|
| MCQ | question stem, then each option A/B/C/D |
| NLI | premise, then hypothesis |
| Reading comprehension | passage, then question, then each option |
| True/false | statement only |
| QA with context | context/passage and question separately when both are translated |

Copy `answer`, `label`, `idx`, and other gold/metadata keys **verbatim from source**.

## Proper nouns, place names, and terms

**Translate into Arabic** — do not leave English (or Chinese) place names and
common nouns untranslated when a natural Arabic form exists.

**Preferred pattern:**

```text
<Arabic name> (<original in source language>)
```

Examples:

- `Lake Erie` → `بحيرة إيري (Lake Erie)`
- `Great Lakes` → `البحيرات العظمى (Great Lakes)`
- `Keukenhof Garden` → `حديقة Keukenhof (Keukenhof Garden)` or fully Arabic if well-known
- `MODIS` → keep acronym if standard, or `موديس (MODIS)` on first mention

Rules:

1. Use established Arabic names for countries, cities, regions, and geographic features when they exist.
2. For less common or technical proper nouns, give the Arabic translation/transliteration first, then `(Original)` in the source language.
3. Coordinates, dates, numbers, and SI units stay as in source.
4. JSON keys, option letters (`A`–`D`), and enum labels in the schema stay unchanged.

## Target language

- **Omani Arabic**, Muscat official register — natural for formal/educational text in Oman.
- Arabic script for all translated body text.

## Output format

- One output row per source row; same keys and row order.
- Translate human-readable string fields only unless the user asks otherwise.
- Skip `reasoning_chain` and eval-only fields unless explicitly requested.
- Record in `meta.json` when useful: `"source_language"`, `"target_language": "ar-om"`.

## Checklist

```
- [ ] Source language confirmed
- [ ] Field isolation map for this dataset schema
- [ ] Each field translated separately (no label, no paired fields visible)
- [ ] Place names and nouns translated to Arabic; originals in () when needed
- [ ] Gold labels copied unchanged from source
- [ ] UTF-8 valid JSON/JSONL, same structure as source
```

## LLM batch translation

- One call per isolated field; do not show other fields or the label in the prompt.
- Do not ask the model to pick the correct answer during translation.
