---
title: "Examples"
description: "Complete OMMS maturity models: a minimal one, a kitchen sink, and the real ODCS and ODPS models."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Examples

Complete maturity models, each validating against
[`schema/omms-json-schema-v0.2.0.json`](https://github.com/bitol-io/open-maturity-model-standard/blob/main/schema/omms-json-schema-v0.2.0.json).

Two are synthetic — a **minimal** model carrying only the required fields, and a **full** model
exercising every construct the standard defines. Two are real: the **ODCS** and **ODPS** maturity
models, the only maturity models in existence, where Level 4 is **AI-Ready**.

| Example | File | What it is |
| ------- | ---- | ---------- |
| Minimal — only required fields | [`minimal.omms.yaml`](minimal.omms.yaml) | One policy, two levels, one criterion each. The smallest document that validates. |
| Full — every construct | [`full.omms.yaml`](full.omms.yaml) | All eight threshold operators, all four metrics, both units, every argument, `type: text`, and both shared structures at several levels. Illustrative, not adoptable. |
| ODCS — data contract maturity | [`odcs-maturity-model.omms.yaml`](odcs-maturity-model.omms.yaml) | Model v2.0.0 for ODCS contracts, targeting ODCS v3.2.0. Five levels: Structural, Descriptive, Governed, **AI-Ready**, Optimized. |
| ODPS — data product maturity | [`odps-maturity-model.omms.yaml`](odps-maturity-model.omms.yaml) | Model v2.0.0 for ODPS data products, targeting ODPS v1.1.0. Five levels: Structural, Descriptive, Governed, **AI-Ready**, Optimized. |

## The ODCS and ODPS models

Both models score an artifact from 1 to 5, cumulatively, and both turn Level 4 into the **AI-Ready**
level: the point at which a machine consumer — a quality engine or an AI agent — can test the artifact
and use it safely. Level 4 is where the `context` block introduced in ODCS v3.2.0 and ODPS v1.1.0
becomes required, which is why both models gate those levels on the newer standard version and cap
artifacts on earlier editions at Level 3.

The five levels come from
["Five Levels Between Chaos and (Almost) AI-Ready Data"](https://dataintelligenceplatform.substack.com/p/five-levels-between-chaos-and-almost)
by Jean-Georges Perrin (2026). Each answers a different question:

| Level | Name | The question it answers |
| ----- | ---- | ----------------------- |
| 1 | Structural | Is it a valid artifact at all? |
| 2 | Descriptive | Can a human understand it? |
| 3 | Governed | Is someone accountable, and is it cross-referenced? |
| 4 | **AI-Ready** | Can a machine test it and use it safely? |
| 5 | Optimized | Is coverage complete and continuously measured? |

Both models open with the same policy: a `status` cap, so a retired artifact scores L0 and a deprecated
one is capped at L1 or L2 no matter how many higher criteria it satisfies. See
[Policies](../policies.md).

The two are worth reading together — they are the same progression applied to a contract and to a
product, and the differences between them are the differences between the two standards. A product
carries no quality dimension of its own (quality is delegated to the contracts it links to), so the
ODPS model asks about observability, dependencies and context instead, and a criterion in one model
often has no counterpart in the other.

> **Note:** a model version (`version: 2.0.0`) is not an OMMS version. Both models are written in the
> OMMS **v0.2.0** syntax while being the second major version of the model itself.
