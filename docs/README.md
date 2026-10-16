---
title: "Open Maturity Model Standard (OMMS)"
description: "A YAML syntax for defining maturity models that evaluate Bitol artifacts such as data contracts and data products."
image: "https://raw.githubusercontent.com/bitol-io/artwork/main/horizontal/color/Bitol_Logo_color.svg"
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Open Maturity Model Standard (OMMS)

The **Open Maturity Model Standard (OMMS)** defines a common syntax for **maturity models** — the
yardsticks that say how complete, governed and operational an artifact is.

OMMS evaluates Bitol artifacts: [ODCS](https://github.com/bitol-io/open-data-contract-standard) data
contracts and [ODPS](https://github.com/bitol-io/open-data-product-standard) data products, and
potentially others. It reuses the ODCS data quality vocabulary (`type`, `metric`, `arguments`,
operators, `unit`) so a maturity model is written the same way a data quality rule is, and it makes
`policies` a first-class concept for expressing the organizational rules that override or constrain
an evaluation.

OMMS defines the **syntax and semantics** of a maturity model. It deliberately does **not** prescribe
level names, level count, or any specific criterion: those are decisions each maturity model makes for
itself.

> ## WORK IN PROGRESS — NOT READY FOR PRODUCTION
>
> OMMS is a **draft standard**, circulated within the Bitol TSC so it can be shaped before release. It is
> **not** a released standard. Expect **breaking changes in every version until v1.0.0**, and incomplete
> areas. Do not build production systems against it.

## Design goals

- **Formal** — maturity models have a syntax tooling can rely on, so an evaluator written for one model can evaluate another.
- **Measurable** — every criterion is factual and quantifiable, expressed with the ODCS data quality vocabulary rather than bespoke constructs.
- **Policy-aware** — organizational rules (a status cap, a sourcing convention) are core model behaviour and get first-class syntax, not a corner of `customProperties`.
- **Non-prescriptive** — the standard fixes how a model is written, never how many levels it has or what they are called.

## Table of Contents

* [Fundamentals](fundamentals.md) — the envelope (`kind`, `apiVersion`, `id`, `name`, `version`, `status`, `targetApplication`) and the `description` object.
* [Policies](policies.md) — rules evaluated before any criteria, including the status cap.
* [Levels](levels.md) — the maturity progression and its cumulative semantics.
* [Criteria](criteria.md) — the observable checks: property paths, metrics, arguments, threshold operators and units.
* [Custom & Other Properties](custom-other-properties.md) — extending OMMS with `customProperties` and `authoritativeDefinitions`.
* [Examples](examples/README.md) — complete maturity models, including the ODCS and ODPS ones.

## Example

A complete, valid OMMS document. Every field below is explained in
[Fundamentals](fundamentals.md), [Policies](policies.md), [Levels](levels.md) and
[Criteria](criteria.md).

```yaml
kind: MaturityModel
apiVersion: v0.2.0
id: 9f3a2c17-8b4d-4e6a-a1f5-7d9c3e8b2a4f
name: example-maturity-model
version: 0.1.0
status: draft
description:
  purpose: Example maturity model demonstrating OMMS v0.2.0 syntax.
tags:
  - example
targetApplication: ODCS
targetApiVersion: v3.1.0

policies:
  - id: POL-C01
    name: statusCap
    property: status
    type: library
    metric: validValues
    arguments:
      caps:
        retired: 0
        active: 5
    description: Retired contracts score L0.

levels:
  - level: 1
    name: Basic
    description: Minimum valid artifact.
    criteria:
      - id: L1-C01
        property: kind
        type: library
        metric: validValues
        mustBe: DataContract
        description: kind must equal DataContract.
```

## Repository layout

| Path | Contents |
| ---- | -------- |
| [`schema/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/schema) | The versioned JSON Schemas. Files using the `.omms.yaml` extension are auto-detected as OMMS. |
| [`docs/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/docs) | This documentation. |
| [`docs/examples/`](examples/README.md) | Complete maturity models. |
| [`tests/negative/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/tests/negative) | Deliberately invalid documents the schema must reject. |
| [`issues.md`](https://github.com/bitol-io/open-maturity-model-standard/blob/main/issues.md) | Questions the standard has not settled. |

OMMS is defined by [RFC-0037](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed with
the Bitol TSC and not yet approved, and is part of the [Bitol](https://bitol.io) family of standards,
alongside [ODCS](https://github.com/bitol-io/open-data-contract-standard),
[ODPS](https://github.com/bitol-io/open-data-product-standard) and
[OORS](https://github.com/bitol-io/open-observability-results-standard).
