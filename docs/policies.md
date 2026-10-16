---
title: "Policies"
description: "Rules evaluated before any level criteria: status caps, sourcing conventions and organizational constraints."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Policies

A **policy** is a rule evaluated *before* any level criteria. Where criteria measure an artifact,
policies constrain the evaluation: they cap the maximum achievable level, enforce a convention, or
state a validation rule that applies across the whole model.

Policies are first-class because they are organizational decisions, not extensions. "Retired contracts
cannot score above L0" is a rule an organization makes about maturity; it belongs in the model, not in
a `customProperties` entry that no evaluator reads.

[Back to TOC](README.md)

## Example

### Minimal

A single policy capping the achievable level by status.

```yaml
policies:
  - id: POL-C01
    property: status
    type: library
    metric: validValues
    arguments:
      caps:
        retired: 0
        active: 5
    description: Retired contracts score L0.
```

### Full

The cap is instant and absolute: a `deprecated` artifact can never score above L2, however many of the
higher-level criteria it satisfies. Policies are evaluated in order, before any criterion is looked at.

```yaml
policies:
  - id: POL-P01
    name: statusCap
    property: status
    type: library
    metric: validValues
    arguments:
      caps:
        retired: 0
        deprecated: 2
        proposed: 1
        draft: 1
        active: 5
    description: >
      Status determines the maximum achievable maturity level.
      Evaluated before all level criteria. The cap is instant
      and absolute.

  - id: POL-P02
    name: selfSourcing
    property: inputPorts
    type: library
    metric: validValues
    arguments:
      where:
        field: name
        in: [self]
      contractIdMustMatch: product.id
      versionMustMatch: product.version
    description: >
      A data product that generates data without external sources
      must use the "self" convention: when inputPort.name is
      "self", contractId must equal the product id and version
      must equal the product version.
```

## Field Descriptions

| Key                        | Type           | UX label               | Required | Description                                                                                                                                                        | Example                    |
| -------------------------- | -------------- | ---------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------- |
| `policies[].id`            | string         | ID                     | Yes      | Unique identifier within the model. Suggested format `POL-{X}{nn}`. See [Policy IDs](#policy-ids).                                                                  | `POL-P01`                  |
| `policies[].name`          | string         | Name                   | No       | Machine-friendly name.                                                                                                                                             | `statusCap`                |
| `policies[].property`      | string         | Property               | Yes      | YAML property path to evaluate, in the artifact being assessed.                                                                                                     | `status`                   |
| `policies[].type`          | string         | Type                   | No       | `library` (machine-evaluable) or `text` (auto-pass). Default is `library`.                                                                                          | `library`                  |
| `policies[].metric`        | string         | Metric                 | No       | Metric name, from the same vocabulary the criteria use. See [Metrics](criteria.md#metrics).                                                                         | `validValues`              |
| `policies[].arguments`     | object         | Arguments              | No       | Parameters for the metric. See [Arguments](criteria.md#arguments).                                                                                                   |                            |
| `policies[].description`   | string         | Description            | No       | Human-readable explanation of the policy and its intent.                                                                                                            | `Status determines the…`   |
| `policies[].tags`          | array          | Tags                   | No       | List of tags.                                                                                                                                                      | `[governance]`             |
| `policies[].authoritativeDefinitions` | array | Authoritative Defs | No    | List of links to sources that provide more details.                                                                                                                 |                            |
| `policies[].customProperties` | array       | Custom Properties      | No       | A list of key/value pairs for custom properties.                                                                                                                    |                            |

## Policy IDs

Policy IDs follow the convention `POL-{X}{nn}`, where `{X}` identifies the artifact the model targets
and `{nn}` is a sequence number:

| ID         | Target                                 |
| ---------- | -------------------------------------- |
| `POL-C01`  | A contract policy (ODCS)               |
| `POL-P01`  | A product policy (ODPS)                |

The convention is *suggested*, not enforced — the schema accepts any string. Policy IDs are unique
within their model and nowhere else: policies from different models never interact, and two models may
each define a `POL-C01` meaning different things.

## The `caps` argument

`caps` is the argument that makes a policy a cap. It maps the values of `property` to the **maximum**
achievable level, and it is the one argument evaluated before anything else in the model.

```yaml
arguments:
  caps:
    retired: 0
    deprecated: 2
    active: 5
```

An artifact whose `status` is `deprecated` is capped at level 2: if every criterion of levels 3, 4 and
5 passes, the artifact still scores **L2**. A level absent from `caps` is unconstrained by that policy.

> **Note:** a cap is not a penalty and not a deduction — it is a ceiling. It never lowers a score that
> would otherwise be lower; it only prevents a score from rising above it.

## Evaluation order

1. Every policy is evaluated, in the order they appear.
2. The most restrictive cap that applies is the ceiling for the whole assessment.
3. Level criteria are then evaluated against the artifact, lowest level first.
4. The artifact's maturity is the highest level for which it satisfies every criterion, never exceeding the ceiling.

[Back to TOC](README.md)
