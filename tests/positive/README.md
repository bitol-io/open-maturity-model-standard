<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Positive tests

Each file here is a **deliberately minimal, valid** OMMS document that exercises one rule the standard
permits. Every one of them **must be accepted** by
[`schema/omms-json-schema-v0.2.1.json`](../../schema/omms-json-schema-v0.2.1.json): a run in which any
of them is rejected is a failure.

They are the counterpart of the [negative tests](../negative/README.md). The negative tests catch a
schema that is too lax; these catch a schema that has become too strict. The examples under
[`docs/examples/`](../../docs/examples/README.md) are valid too, but they mix many rules in one
document, so they do not tell you *which* permitted construct a schema change broke. The header comment
in each file names the rule it guards.

| Fixture | Rule |
| ------- | ---- |
| [`required-fields-only.omms.yaml`](required-fields-only.omms.yaml) | Only `kind`, `apiVersion`, `id`, `status` and `levels` are required. |
| [`presence-and-membership.omms.yaml`](presence-and-membership.omms.yaml) | Presence is `count` with `mustBe: 1`; membership is `valueOf` with `mustBeIn` or `mustNotBeIn`. |
| [`version-thresholds.omms.yaml`](version-thresholds.omms.yaml) | With `valueOf`, ordering operators accept a version string; `mustBe` accepts `latest`. |
| [`all-operators.omms.yaml`](all-operators.omms.yaml) | Each of the ten threshold operators is accepted. |
| [`units.omms.yaml`](units.omms.yaml) | `count` accepts `unit: rows` and `unit: percent`. |
| [`text-without-metric.omms.yaml`](text-without-metric.omms.yaml) | A `type: text` criterion may omit `metric`. |
| [`policy-caps.omms.yaml`](policy-caps.omms.yaml) | A policy caps the achievable level by status with `caps`. |
| [`arguments.omms.yaml`](arguments.omms.yaml) | The arguments `having`, `anyOf`, `where`, `distinct` and `relatesTo`; `in` is in [`units.omms.yaml`](units.omms.yaml). |
| [`extensions-everywhere.omms.yaml`](extensions-everywhere.omms.yaml) | `customProperties` and `authoritativeDefinitions` on the model, a policy, a level and a criterion. |

Run them with the same script CI uses:

```bash
bash scripts/validate-examples.sh
```
