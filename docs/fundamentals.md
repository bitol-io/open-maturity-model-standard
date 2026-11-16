---
title: "Fundamentals"
description: "The envelope of an OMMS maturity model: identity, lifecycle status, description and target standard."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Fundamentals

An OMMS document is a YAML file that describes one **maturity model**. This page covers the envelope:
what the model is, which standard it evaluates, and how it is identified and versioned.

[Back to TOC](README.md)

## Example

### Minimal

Only the required fields: the kind, the standard version, an identifier, the model's lifecycle status,
and at least one level.

```yaml
kind: MaturityModel
apiVersion: v0.2.1
id: 9f3a2c17-8b4d-4e6a-a1f5-7d9c3e8b2a4f
status: draft
levels:
  - level: 1
    name: Basic
    description: Minimum valid artifact.
    criteria:
      - id: L1-C01
        property: kind
        type: library
        metric: valueOf
        mustBe: DataContract
        description: kind must equal DataContract.
```

### Full

Every field in the envelope, with a `description` object saying what the model is for, what it does
not cover, and how to apply it.

```yaml
kind: MaturityModel
apiVersion: v0.2.1
id: 9f3a2c17-8b4d-4e6a-a1f5-7d9c3e8b2a4f
name: odcs-data-contract-maturity-model
version: 2.0.0
status: active
description:
  purpose: >
    Measurable maturity model for ODCS data contracts. Level 4 certifies a
    contract as AI-ready.
  limitations: >
    Criteria are designed for ODCS v3.2.0. Contracts on earlier editions cap
    at Level 3.
  usage: >
    Evaluate a data contract against policies first, then each level's
    criteria sequentially.
tags:
  - maturity-model
  - odcs
  - ai-ready
targetApplication: ODCS
targetApiVersion: v3.2.0
```

## Field Descriptions

| Key                        | Type    | UX label              | Required | Description                                                                                                                                                              | Example                                   |
| -------------------------- | ------- | --------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------- |
| `kind`                     | string  | Kind                  | Yes      | The type of OMMS object. For OMMS, always `MaturityModel`.                                                                                                               | `MaturityModel`                           |
| `apiVersion`               | string  | API Version           | Yes      | Version of the OMMS standard the document uses.                                                                                                                           | `v0.2.1`                                  |
| `id`                       | string  | ID                    | Yes      | A unique identifier for this maturity model (a UUID is recommended).                                                                                                     | `9f3a2c17-8b4d-4e6a-…`                    |
| `name`                     | string  | Name                  | No       | Name of the maturity model.                                                                                                                                              | `odcs-data-contract-maturity-model`       |
| `version`                  | string  | Version               | No       | Version of the **model itself** (SemVer), independent of `apiVersion`.                                                                                                    | `2.0.0`                                   |
| `status`                   | string  | Status                | Yes      | Lifecycle status of the model: `proposed`, `draft`, `active`, `deprecated` or `retired`. See [Status values](#status-values).                                             | `active`                                  |
| `description`              | object  | Description           | No       | Description object containing `purpose`, `limitations` and `usage`.                                                                                                       |                                           |
| `description.purpose`      | string  | Purpose               | No       | Intended purpose of the maturity model.                                                                                                                                  | `Measurable maturity model for ODCS…`     |
| `description.limitations`  | string  | Limitations           | No       | Known limitations, including the target standard version the criteria were written for.                                                                                   | `Criteria are designed for ODCS v3.2.0…`  |
| `description.usage`        | string  | Usage                 | No       | How to apply the model, and in what order its parts are evaluated.                                                                                                        | `Evaluate against policies first…`        |
| `tags`                     | array   | Tags                  | No       | List of tags.                                                                                                                                                            | `[maturity-model, ai-ready]`              |
| `targetApplication`        | string  | Target Application    | No       | The standard this model evaluates, e.g. `ODCS` or `ODPS`.                                                                                                                 | `ODCS`                                    |
| `targetApiVersion`         | string  | Target API Version    | No       | Version of the target standard the criteria were written against. Criteria whose threshold is `latest` resolve against it.                                                | `v3.2.0`                                  |
| `customProperties`         | array   | Custom Properties     | No       | A list of key/value pairs for vendor- or organization-specific extensions. See [Custom & Other Properties](custom-other-properties.md).                                  |                                           |
| `authoritativeDefinitions` | array   | Authoritative Defs    | No       | List of links to sources that provide more details on the model. See [Custom & Other Properties](custom-other-properties.md).                                            |                                           |
| `policies`                 | array   | Policies              | No       | Rules evaluated before any level criteria. See [Policies](policies.md).                                                                                                   |                                           |
| `levels`                   | array   | Levels                | Yes      | List of maturity levels. At least one. See [Levels](levels.md).                                                                                                           |                                           |

> **Note:** `name` and `version` are optional, but omitting them makes a model much harder to refer to
> in a catalog or a report. Supply both on any model that is meant to be used rather than read.

## Status values

`status` describes the maturity *model*, not the artifacts it evaluates. A model's own maturity model —
its `policies` — is usually what reads this field back, most often to cap the achievable level.

| Value        | Meaning                                                                        |
| ------------ | ------------------------------------------------------------------------------ |
| `proposed`   | Put forward, not yet accepted.                                                 |
| `draft`      | Accepted for development; criteria are still moving.                           |
| `active`     | Fit to evaluate artifacts against.                                             |
| `deprecated` | Superseded; still usable, but expect to migrate.                               |
| `retired`    | No longer to be used.                                                          |

[Back to TOC](README.md)
