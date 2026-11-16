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

## How OMMS differs from ODCS and ODPS

An OMMS document looks like a Bitol document, since it shares the same envelope and building blocks.
It has a different shape, though, because it describes something else. A data contract or a data
product describes **an asset**. A maturity model describes **how to evaluate** a data contract or a
data product. It points *at* another document instead of describing data.

### What is the same

- **The envelope**: `kind`, `apiVersion`, `id`, `name`, `version`, `status`, `tags`.
- **The `description` object**, with the same `purpose`, `limitations` and `usage` fields.
- **The extension structures**: `customProperties` and `authoritativeDefinitions`, with the same fields
  and the same recommended `type` values. See [Custom & Other Properties](custom-other-properties.md).
- **The data quality vocabulary**: a criterion is written like an ODCS data quality rule, with `type`,
  `metric`, `arguments`, a threshold operator and `unit`. OMMS adds one metric of its own, `validValues`.
  See [Metrics](criteria.md#metrics).

### What is different

| Aspect | ODCS (data contract) | ODPS (data product) | OMMS (maturity model) |
| ------ | -------------------- | ------------------- | --------------------- |
| `kind` | `DataContract` | `DataProduct` | `MaturityModel` |
| Subject | A dataset and the agreement around it | A data product and its ports | Another Bitol document, named by `targetApplication` and `targetApiVersion` |
| Required fields | `apiVersion`, `kind`, `id`, `version` | `apiVersion`, `kind`, `id` | `apiVersion`, `kind`, `id`, `status`, `levels` |
| `status` | Free string | Free string | Closed list: `proposed`, `draft`, `active`, `deprecated`, `retired` |
| Body | Many independent sections: `schema`, `servers`, `team`, `roles`, `support`, `price`, `slaProperties`… | Ports (`inputPorts`, `outputPorts`, `managementPorts`), `support`, `team` | A single nested spine: `policies`, then `levels[]` → `criteria[]` |
| Order | Sections are unordered | Sections are unordered | Order matters: policies run first, levels are cumulative from the lowest up |
| Ownership and operations | `team`, `roles`, `support`, `domain`, `tenant`, `servers`, creation timestamp | `team`, `support`, `domain`, `tenant`, creation timestamp | None: a model carries no team, support, domain, tenant, servers or timestamps |
| Where a rule applies | `quality[]` nested in the schema object or property it checks, and measured against the **data** | n/a | Anywhere in the evaluated **document**, reached by a `property` path such as `schema[].properties[].description` |
| Rule `type` | `text`, `library`, `sql`, `custom` | n/a | `library` or `text` only, with `text` passing automatically |
| Metrics | `library` metrics `nullValues`, `missingValues`, `invalidValues`, `duplicateValues`, `rowCount`, plus SQL and vendor checks | n/a | Closed to four: `rowCount`, `nullValues` and `invalidValues` from ODCS, which count items, plus `validValues`, specific to OMMS, which reads a value |
| Cross-document references | `dataProduct`, references | `contractId` on ports | `relatesTo: "[DataContract]…"` lets a criterion read a linked document |

In short, ODCS and ODPS are **wide**: many optional sections side by side, any of which may be left
out. OMMS is **deep**: one required list of levels, each holding the criteria an artifact must meet,
behind an optional list of policies that cap the result.

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
| [`tests/positive/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/tests/positive) | Minimal valid documents, one per permitted rule, the schema must accept. |
| [`tests/negative/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/tests/negative) | Deliberately invalid documents the schema must reject. |
| [Issues](https://github.com/bitol-io/open-maturity-model-standard/issues) | Questions the standard has not settled, tracked on GitHub. |

OMMS is defined by [RFC-0037](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed with
the Bitol TSC and not yet approved, and is part of the [Bitol](https://bitol.io) family of standards,
alongside [ODCS](https://github.com/bitol-io/open-data-contract-standard),
[ODPS](https://github.com/bitol-io/open-data-product-standard) and
[OORS](https://github.com/bitol-io/open-observability-results-standard).
