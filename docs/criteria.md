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
being evaluated, a `metric` that says what to measure there, optional `arguments` to narrow what gets
measured, and one threshold operator that says what the result must satisfy.

OMMS has two metrics: `valueOf` reads a value, `count` counts items. See [Metrics](#metrics).

[Back to TOC](README.md)

## Example

### Minimal

A criterion that reads one value and compares it to the threshold.

```yaml
- id: L1-C01
  property: kind
  type: library
  metric: valueOf
  mustBe: DataContract
  description: kind must equal DataContract.
```

### Full

A criterion that counts items across a collection and states a percentage threshold.

```yaml
- id: L2-C04
  property: schema[].properties[].description
  type: library
  metric: count
  mustBeGreaterThan: 80
  unit: percent
  description: More than 80% of properties have a description.
```

## Field Descriptions

| Key                        | Type    | UX label               | Required | Description                                                                                                                                                                     | Example                             |
| -------------------------- | ------- | ---------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| `criteria[].id`            | string  | ID                     | Yes      | Unique identifier. Format `L{n}-{X}{nn}`. See [Criterion IDs](#criterion-ids).                                                                                                   | `L4-P04`                            |
| `criteria[].property`      | string  | Property               | Yes      | YAML property path to evaluate, in the artifact being assessed. Array steps use bracket notation.                                                                                | `schema[].properties[].description` |
| `criteria[].type`          | string  | Type                   | No       | `library` (machine-evaluable) or `text` (auto-pass). Default is `library`. See [`library` and `text`](#library-and-text).                                                         | `library`                           |
| `criteria[].metric`        | string  | Metric                 | Yes (unless `type` is `text`) | `valueOf` or `count`. See [Metrics](#metrics).                                                                                                 | `count`                             |
| `criteria[].arguments`     | object  | Arguments              | No       | Parameters that narrow what is measured. See [Arguments](#arguments).                                                                                                            |                                     |
| `criteria[].unit`          | string  | Unit                   | No       | `rows` or `percent`, with `count` only. See [Units](#units).                                                                                                                     | `percent`                           |
| `criteria[].description`   | string  | Description            | No       | Human-readable explanation of the criterion, and of what a passing artifact looks like.                                                                                          | `Purpose is documented.`            |
| `<operator>`               | any     | *(the operator key)*   | Yes (unless `type` is `text`) | The threshold, named by exactly one of the ten operators. See [Threshold operators](#threshold-operators).                                     | `mustBeGreaterOrEqualTo: 1`         |
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

| Metric    | Returns | Use it for                                                              |
| --------- | ------- | ----------------------------------------------------------------------- |
| `valueOf` | A value | A single value: "is `kind` `DataContract`?", "is `apiVersion` at least `v3.2.0`?" |
| `count`   | A count | Presence and coverage: "is `version` set?", "do 80% of properties have a description?" |

The set is closed: the schema accepts these two and no others. A different measurement is expressed
with [arguments](#arguments), not with a new metric.

### `valueOf`

`valueOf` reads the value at `property` and compares it to the threshold. It is the metric for a
single value, so `property` points at one value, not at a collection, and the criterion carries no
`unit`.

```yaml
- id: L4-C01
  property: apiVersion
  type: library
  metric: valueOf
  mustBeGreaterOrEqualTo: v3.2.0
  description: The contract uses ODCS v3.2.0 or newer.
```

When the value must be one of several, use `mustBeIn`; when it must be none of several, `mustNotBeIn`:

```yaml
- id: L1-C03
  property: status
  type: library
  metric: valueOf
  mustBeIn: [proposed, draft, active, deprecated, retired]
  description: Status holds a valid ODCS status value.
```

A missing value fails every `valueOf` threshold except `mustNotBe` and `mustNotBeIn`. To require a
value to be present, whatever it is, use `count`.

### `count`

`count` counts the items present at `property`: an item that is null or empty is not counted. The
result is a number of items with `unit: rows` (the default), or their share of all the items the path
reaches with `unit: percent`.

| Question | Criterion |
| -------- | --------- |
| Is `version` set? | `property: version`, `metric: count`, `mustBe: 1` |
| Is at least one output port defined? | `property: outputPorts[]`, `metric: count`, `mustBeGreaterOrEqualTo: 1` |
| Do all schema objects have a name? | `property: schema[].name`, `metric: count`, `mustBe: 100`, `unit: percent` |
| Are at least half of the properties classified? | `property: schema[].properties[].classification`, `metric: count`, `mustBeGreaterOrEqualTo: 50`, `unit: percent` |

To count only the items whose value is in a list, use the `in` argument:

```yaml
- id: L5-C03
  property: quality[].type
  type: library
  metric: count
  arguments:
    in: [library, sql]
  mustBeGreaterOrEqualTo: 80
  unit: percent
  description: ">= 80% of quality rules are executable (library or sql)."
```

> **Note:** `count` counts what is present, so it expresses "at least N present" or "at least N% present",
> not "at most N missing". When a tolerance matters, state it as a percentage.

## Threshold operators

A `library` criterion states **exactly one** operator. Its name is the comparison; its value is what to
compare against.

| Operator                 | Meaning                    | With                |
| ------------------------ | -------------------------- | ------------------- |
| `mustBe`                 | Exact match                | `valueOf`, `count`  |
| `mustNotBe`              | Must not equal             | `valueOf`, `count`  |
| `mustBeGreaterThan`      | Strict lower bound         | `valueOf`, `count`  |
| `mustBeGreaterOrEqualTo` | Inclusive lower bound      | `valueOf`, `count`  |
| `mustBeLessThan`         | Strict upper bound         | `valueOf`, `count`  |
| `mustBeLessOrEqualTo`    | Inclusive upper bound      | `valueOf`, `count`  |
| `mustBeBetween`          | Inclusive range [min, max] | `valueOf`, `count`  |
| `mustNotBeBetween`       | Exclusive range [min, max] | `valueOf`, `count`  |
| `mustBeIn`               | One of the listed values   | `valueOf`           |
| `mustNotBeIn`            | None of the listed values  | `valueOf`           |

The ordering operators accept a **version string** as well as a number, which is how a model gates on
the version of the standard it is evaluating (see [`valueOf`](#valueof)). The comparison strips the `v`
prefix, so `v3.2.0 >= v3.1.0` holds.

`mustBe` additionally accepts the special value **`latest`**, which instructs the evaluator to resolve
to the current version of the `targetApplication` standard, falling back to the model's
`targetApiVersion`:

```yaml
- id: L5-C01
  property: apiVersion
  type: library
  metric: valueOf
  mustBe: latest
  description: The contract tracks the latest ODCS release.
```

## Units

`unit` applies to `count` only.

| Unit      | Description                                              |
| --------- | -------------------------------------------------------- |
| `rows`    | Number of items counted. The default.                    |
| `percent` | Items counted, as a share of all the items the path reaches (0-100). |

The same threshold changes meaning with the unit: `mustBeGreaterOrEqualTo: 20` with `unit: rows`
asks for at least 20 items, with `unit: percent` for at least 20% of them.

## Arguments

Arguments narrow what is measured.

| Key           | Type            | Description                                                                                                               |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `having`      | string or array | Count only items that have these fields present.                                                                          |
| `anyOf`       | array           | Count only items that have at least one of these fields.                                                                  |
| `in`          | array           | Count only items whose value is in this list.                                                                             |
| `where`       | object          | Filter items. Contains `field` (string), `in` (array), and/or `isNotNull` (boolean).                                       |
| `distinct`    | boolean         | Count unique values only.                                                                                                 |
| `relatesTo`   | string          | Dependency reference. Bracket notation (`[DataContract]`, `[DataProduct]`) for cross-artifact, plain path for intra-file. |
| `caps`        | object          | Map status values to maximum achievable levels (policies only). See [Policies](policies.md#the-caps-argument).             |

Two further arguments support the self-sourcing convention: `contractIdMustMatch` and
`versionMustMatch` name the property paths a referencing `contractId` and `version` must equal.

Arguments are usually combined. Counting distinct data quality dimensions, restricted to the seven
the standard defines:

```yaml
- id: L5-C06
  property: quality[].dimension
  type: library
  metric: count
  arguments:
    distinct: true
    in:
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
  `metric`, and compares the result against the threshold.
- **`text`** — auto-pass. The criterion documents an expectation the evaluator does not resolve, and
  it scores as met. It is how a model records something a machine cannot check yet without blocking
  every artifact from reaching the level. `metric` and the operator are optional.

A `text` criterion often still carries a `metric` and a threshold, because they tell a reader — and a
future evaluator — what the check would be:

```yaml
- id: L1-C11
  property: jsonSchemaValidation
  type: text
  metric: count
  mustBe: 0
  unit: rows
  description: YAML passes ODCS JSON Schema validation with 0 errors.
```

> **Note:** `text` criteria are a deliberate hole in a model. A model that leans on them measures less
> than its level names claim, so it is worth knowing how many a model has before trusting a score.

## Evaluating a criterion

The walkthrough below applies to every `count` criterion; only the numbers change.

**Criterion:**

```yaml
- id: L2-C04
  property: schema[].properties[].description
  type: library
  metric: count
  mustBeGreaterThan: 80
  unit: percent
  description: More than 80% of properties have a description.
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

1. Follow the path across all schema objects → 5 properties
2. `metric: count` → count properties whose `description` is present → 4 (all but `sku`)
3. `unit: percent` → (4 / 5) × 100 = 80%
4. `mustBeGreaterThan: 80` → is 80 > 80?
5. **Result: FAIL** (actual: 80%, required: > 80%)

## Cross-artifact criteria

A `relatesTo` written in bracket notation resolves against a *different* artifact, reached by following
an identifier in the one being evaluated. It is how a model asks a question about a data product that
only its contracts can answer.

```yaml
- id: L3-P06
  property: outputPorts[].contractId
  type: library
  metric: count
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
reads its `status`, then `where` filters to the active ones and `count` counts them. A resolver is
an input to the evaluation, not something the standard can supply: the evaluator must know where the
contracts live. A `relatesTo` whose artifact cannot be resolved is an unresolved criterion, not a
failure — treat it as `text` rather than as a zero.

[Back to TOC](README.md)
