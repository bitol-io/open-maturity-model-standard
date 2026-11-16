---
title: "Changelog: Open Maturity Model Standard (OMMS)"
description: "Home of Open Maturity Model Standard (OMMS) documentation."
image: "https://raw.githubusercontent.com/bitol-io/artwork/main/horizontal/color/Bitol_Logo_color.svg"
---

This document tracks the history and evolution of the **Open Maturity Model Standard**.

# v0.2.0 - DRAFT

The first version of OMMS to exist as a repository. It is the schema-and-documentation form of
[RFC-0037](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed with the TSC and not yet
approved.

* **Defines** the OMMS document: a `MaturityModel` with an envelope (`kind`, `apiVersion`, `id`, `name`, `version`, `status`, `description`, `tags`, `targetApplication`, `targetApiVersion`), `policies`, and `levels`. `kind`, `apiVersion`, `id`, `status` and `levels` are required; `name` and `version` are not, though a model meant to be used rather than read should supply both.
* **Adds `policies`** as a first-class concept, evaluated before any level criteria. A policy is what expresses an organizational rule — most often a `caps` map from status to maximum achievable level, so a `deprecated` artifact cannot score above L2 however many higher criteria it satisfies. Policy IDs follow the suggested convention `POL-{X}{nn}`. See [Policies](docs/policies.md).
* **Adds `levels`** as a cumulative progression: an artifact qualifies for a level only when it satisfies all its criteria and all criteria of every lower level, which makes the first failing level the ceiling. The standard prescribes no level names, no level count and no ordering. See [Levels](docs/levels.md).
* **Adds criteria** expressed in the ODCS data quality vocabulary: a `property` path (with bracket notation for arrays), a `metric`, optional `arguments`, one threshold operator, and an optional `unit`. Criterion IDs follow the enforced format `L{n}-{X}{nn}`. See [Criteria](docs/criteria.md).
* **Defines four metrics.** `rowCount`, `nullValues` and `invalidValues` come from ODCS and count items. `validValues` is specific to OMMS: it reads the value at `property` and compares it to the threshold, which is what lets a criterion test a single value or gate on a version. ODCS dropped `validValues` as a rule name in v3.1.0 in favour of `invalidValues`; OMMS keeps it deliberately. The set is closed: a new measurement is expressed with arguments (`distinct`, `having`, `where`) rather than a new metric name.
* **Adds the eight threshold operators**, aligned with ODCS: `mustBe`, `mustNotBe`, `mustBeGreaterThan`, `mustBeGreaterOrEqualTo`, `mustBeLessThan`, `mustBeLessOrEqualTo`, `mustBeBetween`, `mustNotBeBetween`. The ordering operators accept a version string as well as a number, so a model can gate on the version of the standard it evaluates; `mustBe: latest` resolves to the current version of the `targetApplication` standard.
* **Adds arguments** shaping the collection before the metric counts it: `having`, `anyOf`, `where` (`field`, `in`, `isNotNull`), `distinct`, `relatesTo` (bracket notation for cross-artifact references), `validValues` and `caps`, plus `contractIdMustMatch` and `versionMustMatch` for the self-sourcing convention.
* **Adds** the shared Bitol `customProperties` and `authoritativeDefinitions` structures, available on the model, a policy, a level and a criterion. See [Custom & Other Properties](docs/custom-other-properties.md).
* **Ships four examples** — a `minimal` model, a `full` model exercising every construct in the standard, and the **ODCS** and **ODPS** maturity models at v2.0.0, in which **Level 4 is the AI-Ready level**. The two real models are the only maturity models in existence; they were moved here from the Bitol Workbench, where they had been living as private seeds.
* **Adds** the negative-test fixtures under `tests/negative/` — invalid documents the schema must reject — wired into `scripts/validate-examples.sh`.
* **Records** two questions RFC-0037 does not settle, found by validating the real models against the schema, as GitHub issues [#4](https://github.com/bitol-io/open-maturity-model-standard/issues/4) and [#5](https://github.com/bitol-io/open-maturity-model-standard/issues/5).
