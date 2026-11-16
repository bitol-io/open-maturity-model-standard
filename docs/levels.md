---
title: "Levels"
description: "The maturity progression: cumulative levels, each with its own criteria."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Levels

A **level** is one step in the maturity progression. Each level has a name, a description, and the
criteria an artifact must satisfy to reach it.

Levels are **cumulative**: an artifact qualifies for a level only when it satisfies every criterion of
that level *and* every criterion of every lower level. There is no way to trade one level's criteria
against another's.

OMMS does not prescribe level names, level count, or an ordering convention. A model may have three
levels or seven, and may call its top level anything. The standard fixes only how levels are written.

[Back to TOC](README.md)

## Example

### Minimal

One level with one criterion.

```yaml
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

Two levels, showing how a model carries an expectation forward: level 2 assumes level 1 already
passed, so it does not repeat the criteria that establish validity.

```yaml
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

  - level: 2
    name: Enriched
    description: Artifact has business context.
    criteria:
      - id: L2-C01
        property: description.purpose
        type: library
        metric: count
        mustBe: 1
        unit: rows
        description: Purpose is documented.
```

## Field Descriptions

| Key                        | Type    | UX label              | Required | Description                                                                                                                                                    | Example                       |
| -------------------------- | ------- | --------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| `levels[].level`           | integer | Level                 | Yes      | Level number (1, 2, 3…).                                                                                                                                        | `1`                           |
| `levels[].name`            | string  | Name                  | Yes      | Human-readable name.                                                                                                                                            | `AI-Ready`                    |
| `levels[].description`     | string  | Description           | Yes      | What this level means. This could be displayed to the end-user. Be cognizant of the verbiage.                                                                   | `Minimum valid artifact.`     |
| `levels[].criteria`        | array   | Criteria              | Yes      | List of criteria for this level. At least one. See [Criteria](criteria.md).                                                                                     |                               |
| `levels[].tags`            | array   | Tags                  | No       | List of tags.                                                                                                                                                   | `[structural]`                |
| `levels[].authoritativeDefinitions` | array | Authoritative Defs | No     | List of links to sources that provide more details on this level.                                                                                               |                               |
| `levels[].customProperties` | array  | Custom Properties     | No       | A list of key/value pairs for custom properties.                                                                                                                |                               |

## Naming levels

The level names in the two shipped maturity models — **Structural**, **Descriptive**, **Governed**,
**AI-Ready**, **Optimized** — come from
["Five Levels Between Chaos and (Almost) AI-Ready Data"](https://dataintelligenceplatform.substack.com/p/five-levels-between-chaos-and-almost)
by Jean-Georges Perrin (2026), and are offered as a worked example rather than a recommendation. They
progress from *"the artifact is valid"*, through *"a human can understand it"* and *"someone is
accountable"*, to *"a machine can use it safely"* and *"coverage is complete"*.

Two properties are worth copying from that progression, whatever names a model chooses:

- **Each level answers a different question.** "Is it valid?", "Does a human understand it?", "Can a
  machine act on it?" — a level that only adds more of the previous level's checks measures degree,
  not maturity.
- **The description is user-facing.** It is the sentence a report or a catalog shows next to a score,
  so it should read as an answer, not as an internal note.

## Cumulative evaluation

Because levels are cumulative, the **first level at which any criterion fails is the ceiling**. An
evaluator may stop there: nothing above it can pass, since every higher level presupposes it.

```
L1 ✓  L2 ✓  L3 ✗  L4 —    →  the artifact scores L2
```

Criteria may be repeated across levels with a tighter threshold, which is how a model expresses "more
of the same, in better shape" without inventing new checks:

```yaml
# Level 2 — at least half of the properties are described
- id: L2-C04
  property: schema[].properties[].description
  type: library
  metric: count
  mustBeGreaterThan: 50
  unit: percent

# Level 5 — every property is described
- id: L5-C04
  property: schema[].properties[].description
  type: library
  metric: count
  mustBe: 100
  unit: percent
```

> **Note:** cumulative evaluation makes level *order* meaningful even though the standard does not
> prescribe it. A model that puts a cheap structural check at level 4 and an expensive contextual one
> at level 1 will still evaluate correctly, and will still read oddly.

[Back to TOC](README.md)
