<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Negative tests

Each file here is a **deliberately invalid** OMMS document. Every one of them **must be rejected** by
[`schema/omms-json-schema-v0.2.0.json`](../../schema/omms-json-schema-v0.2.0.json) — a run in which any
of them validates is a failure.

Their counterpart is the [positive tests](../positive/README.md). They guard the rules the positive examples cannot: that the schema actually enforces what the standard
says. The header comment in each file names the specific violation.

| Fixture | Violation |
| ------- | --------- |
| [`missing-levels.omms.yaml`](missing-levels.omms.yaml) | Required top-level `levels` array omitted. |
| [`invalid-kind.omms.yaml`](invalid-kind.omms.yaml) | `kind: DataContract` instead of the constant `MaturityModel`. |
| [`invalid-status.omms.yaml`](invalid-status.omms.yaml) | `status` outside the permitted set (`proposed`, `draft`, `active`, `deprecated`, `retired`). |
| [`library-without-metric.omms.yaml`](library-without-metric.omms.yaml) | A `type: library` criterion that names no `metric`. |
| [`invalid-unit.omms.yaml`](invalid-unit.omms.yaml) | `unit: items` — a pre-OMMS value RFC-0037's migration notes retire. |
| [`invalid-operator.omms.yaml`](invalid-operator.omms.yaml) | `mustBeGreaterThanOrEqualTo`, which is not one of the eight threshold operators. |

Run them with the same script CI uses:

```bash
bash scripts/validate-examples.sh
```
