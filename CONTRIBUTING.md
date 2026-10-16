---
title: "Contributing: Open Maturity Model Standard (OMMS)"
description: "How you can contribute to the Open Maturity Model Standard (OMMS)."
image: "https://raw.githubusercontent.com/bitol-io/artwork/main/horizontal/color/Bitol_Logo_color.svg"
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Contributing to Open Maturity Model Standard

Thank you for your interest in contributing to Open Maturity Model Standard (OMMS). Please refer to the
[TSC contributing guidelines](https://github.com/bitol-io/tsc/blob/main/CONTRIBUTING.md).

## Branching and merging

`main` is protected by a repository rule that requires changes to land through a **pull request** —
direct pushes to `main` are rejected.

Reviews are not required. Any TSC member with write access can merge their own pull request as soon as
the checks below are green; a second reviewer is welcome but never a gate. `main` cannot be
force-pushed or deleted, and pull requests are merged with a **merge commit** — `main` does not accept
squash or rebase merges.

The `validate-examples` workflow runs on every pull request and verifies every example against the
JSON schema, and asserts that the deliberately-invalid fixtures under `tests/negative/` are rejected.
It is a **required check**: a pull request cannot be merged until it passes.

Documentation lives in `docs/` and is built with mkdocs. No documentation site is published yet: OMMS
is a private repository on a GitHub Free organisation, where Pages is unavailable.

## Adding or changing a maturity model

The maturity models under [`docs/examples/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/docs/examples) are as much a part of this repository as
the schema is: they are what the syntax is tested against. If you change one, run the validation
locally before opening a pull request:

```bash
bash scripts/validate-examples.sh
```

## Proposing a change to the standard

A change to what OMMS permits — a new argument, a new operator, a new metric — is an **RFC**, not a
pull request. File it in the [TSC repository](https://github.com/bitol-io/tsc) following
[the RFC process](https://github.com/bitol-io/tsc/blob/main/rfcs/README.md). The schema here follows
the approved RFCs; it does not lead them.

Questions RFC-0037 has not settled are collected in [`issues.md`](https://github.com/bitol-io/open-maturity-model-standard/blob/main/issues.md).
