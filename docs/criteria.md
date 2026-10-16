---
title: "Criteria"
description: "The observable checks within a level: property paths, metrics, arguments, threshold operators and units."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Criteria

A **criterion** is one measurable check inside a level. It names a `property` to look at in the artifact
being evaluated, a `metric` to compute over it, optional `arguments` to shape what gets collected, and
a threshold operator that says what the measured value must satisfy.

Criteria use the ODCS data quality vocabulary — the same `metric`, `arguments`, operators and `unit` a
data quality rule uses. OMMS introduces no new metrics.

[Back to TOC](README.md)

## Example

### Minimal

A criterion that reads one property, applies one metric, and states one threshold.

```yaml
- id: L1-C01
  property: kind
  type: library
  metric: validValues
  mustBe: DataContract
  description: kind must equal DataContract.
```

### Full

The same shape with arguments shaping the collection, and a percentage threshold.

```yaml
- id: L2-C04
  property: schema[].properties[].description
  type: library
  metric: nullValues
  mustBeLessThan: 20
  unit: percent
  description: ">= 80% of properties have descriptions (< 20% null)."
```

## Field Descriptions

| Key                        | Type    | UX label               | Required | Description                                                                                                                                                                     | Example                             |
| -------------------------- | ------- | ---------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `criteria[].id`            | string  | ID                     | Yes      | Unique identifier. Format `L{n}-{X}{nn}`. See [Criterion IDs](#criterion-ids).                                                                                                   | `L4-P04`                            |
| `criteria[].property`      | string  | Property               | Yes      | YAML property path to evaluate, in the artifact being assessed. Array steps use bracket notation.                                                                                | `schema[].properties[].description` |
| `criteria[].type`          | string  | Type                   | No       | `library` (machine-evaluable) or `text` (auto-pass). Default is `library`. See [`library` and `text`](#library-and-text).                                                         | `library`                           |
| `criteria[].metric`        | string  | Metric                 | Yes (unless `type` is `text`) | Metric name. See [Metrics](#metrics).                                                                                                        | `nullValues`                        |
| `criteria[].arguments`     | object  | Arguments              | No       | Parameters for the metric. See [Arguments](#arguments).                                                                                                                          |                                     |
| `criteria[].unit`          | string  | Unit                   | No       | `rows` or `percent`. See [Units](#units).                                                                                                                                        | `percent`                           |
| `criteria[].description`   | string  | Description            | No       | Human-readable explanation of the criterion, and of what a passing artifact looks like.                                                                                          | `Purpose is documented.`            |
| `<operator>`               | any     | *(the operator key)*   | No       | The threshold, named by one of the eight operators. See [Threshold operators](#threshold-operators).                                                                              | `mustBeGreaterOrEqualTo: 1`         |
| `criteria[].tags`          | array   | Tags                   | No       | List of tags.                                                                                                                                                                    | `[quality]`                         |
| `criteria[].authoritativeDefinitions` | array | Authoritative Defs | No     | List of links to sources that provide more details on this criterion.                                                                                                            |                                     |
| `criteria[].customProperties` | array | Custom Properties     | No       | A list of key/value pairs for custom properties.                                                                                                                                 |                                     |

## Criterion IDs

Criterion IDs follow the convention `L{n}-{X}{nn}`:

- `{n}` is the level number,
- `{X}` identifies the artifact the model targets (`C` for a contract, `P` for a product),
- `{nn}` is a sequence number within that level.

The schema enforces the shape, so `L4-P04` is valid and `L4-04` is not. IDs are unique within the
model. They are **not** stable across a major version of a model: v2.0.0 of the ODCS model moved what
was `L4-C03` to `L3-C13`, so never compare two model versions by ID.

## Metrics

OMMS reuses the ODCS library metrics and introduces none of its own.

| Metric          | Description                          |
| --------------- | ------------------------------------ |
| `rowCount`      | Counts items in a collection         |
| `nullValues`    | Counts null or empty values          |
| `invalidValues` | Counts values not in an allowed set  |
| `validValues`   | Checks values against an allowed set |

The set is closed: the schema accepts these four and no others. A model that needs a different
measurement expresses it with the existing metrics plus [arguments](#arguments) — `distinct: true` on
`rowCount` for a unique-value count, `having` for a presence count — rather than by naming a new
metric.

## Threshold operators

Exactly one operator states the threshold. Its name is the comparison; its value is what to compare
against.

| Operator                 | Meaning                    |
| ------------------------ | -------------------------- |
| `mustBe`                 | Exact match                |
| `mustNotBe`              | Must not equal             |
| `mustBeGreaterThan`      | Strict lower bound         |
| `mustBeGreaterOrEqualTo` | Inclusive lower bound      |
| `mustBeLessThan`         | Strict upper bound         |
| `mustBeLessOrEqualTo`    | Inclusive upper bound      |
| `mustBeBetween`          | Inclusive range [min, max] |
| `mustNotBeBetween`       | Exclusive range [min, max] |

The ordering operators accept a **version string** as well as a number, which is how a model gates on
the version of the standard it is evaluating:

```yaml
- id: L4-C01
  property: apiVersion
  type: library
  metric: validValues
  mustBeGreaterOrEqualTo: v3.2.0
  description: The contract uses ODCS v3.2.0 or newer.
```

The comparison strips the `v` prefix, so `v3.2.0 >= v3.1.0` holds. There is **no "one-of" operator**:
to accept "this version or newer", use the floor `mustBeGreaterOrEqualTo`.

`mustBe` additionally accepts the special value **`latest`**, which instructs the evaluator to resolve
to the current version of the `targetApplication` standard, falling back to the model's
`targetApiVersion`:

```yaml
- id: L5-C01
  property: apiVersion
  type: library
  metric: validValues
  mustBe: latest
  description: The contract tracks the latest ODCS release.
```

## Units

| Unit      | Description        |
| --------- | ------------------ |
| `rows`    | Count of items     |
| `percent` | Percentage (0-100) |

`rows` is a count and `percent` is a ratio of the collected items, so the same criterion changes
meaning with the unit — `mustBeLessThan: 20` with `unit: rows` allows up to 19 undescribed properties,
with `unit: percent` it allows up to 19% of them.

## Arguments

Arguments shape the collection before the metric counts it.

| Key           | Type            | Description                                                                                                               |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `having`      | string or array | Item must have these fields present.                                                                                      |
| `anyOf`       | array           | Item must have at least one of these fields.                                                                              |
| `where`       | object          | Filter items. Contains `field` (string), `in` (array), and/or `isNotNull` (boolean).                                       |
| `distinct`    | boolean         | Count unique values only.                                                                                                 |
| `relatesTo`   | string          | Dependency reference. Bracket notation (`[DataContract]`, `[DataProduct]`) for cross-artifact, plain path for intra-file. |
| `validValues` | array           | Restrict to values in this list.                                                                                          |
| `caps`        | object          | Map status values to maximum achievable levels (policies only). See [Policies](policies.md#the-caps-argument).             |

Two further arguments support the self-sourcing convention: `contractIdMustMatch` and
`versionMustMatch` name the property paths a referencing `contractId` and `version` must equal.

Arguments are usually combined. Counting distinct data quality dimensions, restricted to the seven
the standard defines:

```yaml
- id: L5-C06
  property: quality[].dimension
  type: library
  metric: rowCount
  arguments:
    distinct: true
    validValues:
      - accuracy
      - completeness
      - conformity
      - consistency
      - coverage
      - timeliness
      - uniqueness
  mustBeGreaterOrEqualTo: 3
  unit: rows
  description: ">= 3 of 7 Data QoS dimensions represented across all quality rules."
```

## `library` and `text`

`type` decides whether an evaluator resolves the criterion on its own.

- **`library`** (the default) — machine-evaluable. The evaluator navigates `property`, applies
  `metric` over the collected items, and compares against the threshold.
- **`text`** — auto-pass. The criterion documents an expectation the evaluator does not resolve, and
  it scores as met. It is how a model records something a machine cannot check yet without blocking
  every artifact from reaching the level.

A `text` criterion still carries a `metric` and often a threshold, because the two fields are what
tell a reader — and a future evaluator — what the check would be:

```yaml
- id: L1-C11
  property: jsonSchemaValidation
  type: text
  metric: rowCount
  mustBe: 0
  unit: rows
  description: YAML passes ODCS JSON Schema validation with 0 errors.
```

> **Note:** `text` criteria are a deliberate hole in a model. A model that leans on them measures less
> than its level names claim, so it is worth knowing how many a model has before trusting a score.

## Evaluating a criterion

The walkthrough below applies to every criterion; only the numbers change.

**Criterion:**

```yaml
- id: L2-C04
  property: schema[].properties[].description
  type: library
  metric: nullValues
  mustBeLessThan: 20
  unit: percent
  description: ">= 80% of properties have descriptions (< 20% null)."
```

**Artifact:**

```yaml
schema:
  - name: product
    properties:
      - name: product_id
        description: Unique identifier for the product.
      - name: unit_price
        description: Price per unit in USD.
      - name: sku
      - name: category
        description: Product category.
      - name: created_at
        description: Timestamp when product was created.
```

**Evaluation:**

1. Iterate all properties across all schema objects → 5 properties
2. `metric: nullValues` → count properties where `description` is null or empty → 1 (`sku`)
3. `unit: percent` → (1 / 5) × 100 = 20%
4. `mustBeLessThan: 20` → is 20 < 20?
5. **Result: FAIL** (actual: 20% null, required: < 20%)

## Cross-artifact criteria

A `relatesTo` written in bracket notation resolves against a *different* artifact, reached by following
an identifier in the one being evaluated. It is how a model asks a question about a data product that
only its contracts can answer.

```yaml
- id: L3-P06
  property: outputPorts[].contractId
  type: library
  metric: rowCount
  arguments:
    relatesTo: "[DataContract].status"
    where:
      field: status
      in: [active]
  mustBeGreaterOrEqualTo: 1
  unit: rows
  description: >
    At least one outputPort.contractId references an ODCS
    DataContract with status: active.
```

For each `contractId` collected from the product, the evaluator resolves the linked ODCS contract and
reads its `status`, then `where` filters to the active ones and `rowCount` counts them. A resolver is
an input to the evaluation, not something the standard can supply: the evaluator must know where the
contracts live. A `relatesTo` whose artifact cannot be resolved is an unresolved criterion, not a
failure — treat it as `text` rather than as a zero.

[Back to TOC](README.md)
