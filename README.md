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
`policies` a first-class concept for expressing the organizational rules that override or constrain an
evaluation.

OMMS defines the syntax and semantics of a maturity model. It deliberately does **not** prescribe level
names, level count, or any specific criterion: those are decisions each maturity model makes for
itself. As an open format developed within the [Bitol](https://bitol.io) community, OMMS is
human-readable and follows the same philosophy as
[ODCS](https://github.com/bitol-io/open-data-contract-standard),
[ODPS](https://github.com/bitol-io/open-data-product-standard) and
[OORS](https://github.com/bitol-io/open-observability-results-standard), integrating naturally into
existing data workflows.

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

## Documentation

- [Fundamentals](docs/fundamentals.md) — the envelope: identity, lifecycle status, description, target standard.
- [Policies](docs/policies.md) — rules evaluated before any criteria, including the status cap.
- [Levels](docs/levels.md) — the maturity progression and its cumulative semantics.
- [Criteria](docs/criteria.md) — property paths, metrics, arguments, threshold operators and units.
- [Custom & Other Properties](docs/custom-other-properties.md) — extending OMMS.
- [Examples](docs/examples/README.md) — sample maturity models, including the real ODCS and ODPS ones.

The JSON Schema lives in [`schema/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/schema).
Files using the `.omms.yaml` extension are auto-detected as OMMS.

The documentation is built with mkdocs from [`docs/`](docs/README.md). No documentation site is
published yet: OMMS is a private repository on a GitHub Free organisation, where Pages is unavailable.

## Example

```yaml
kind: MaturityModel
apiVersion: v0.2.0
id: 9f3a2c17-8b4d-4e6a-a1f5-7d9c3e8b2a4f
name: example-maturity-model
version: 0.1.0
status: draft
targetApplication: ODCS
targetApiVersion: v3.1.0
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

## Known open issues

Two questions RFC-0037 does not settle were found by validating the only two maturity models that
exist against the schema. Both are recorded — with the options and a recommendation — in
[`issues.md`](https://github.com/bitol-io/open-maturity-model-standard/blob/main/issues.md).

## Contributing

OMMS is governed by the Bitol Technical Steering Committee (TSC). See [CONTRIBUTING.md](CONTRIBUTING.md)
and the [TSC contributing guidelines](https://github.com/bitol-io/tsc/blob/main/CONTRIBUTING.md). The
defining RFC is [RFC-0037 (OMMS)](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed
with the TSC and not yet approved.

## License

This project is licensed under the Apache License 2.0 — see [LICENSE](https://github.com/bitol-io/open-maturity-model-standard/blob/main/LICENSE).
