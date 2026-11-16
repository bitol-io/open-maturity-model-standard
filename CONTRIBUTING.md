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

> **Not currently enforced.** OMMS is a private repository on a GitHub Free organisation, and GitHub
> refuses both repository rulesets and classic branch protection for a private repository on that plan.
> Everything below is therefore the policy this project follows, not a rule the server applies — a
> direct push to `main` will succeed. It becomes enforceable, unchanged, the moment the repository is
> public or the organisation is upgraded.

Changes land on `main` through a **pull request** — direct pushes to `main` are rejected.

Reviews are not required. Any TSC member with write access can merge their own pull request as soon as
the checks below are green; a second reviewer is welcome but never a gate. `main` is never
force-pushed or deleted, and pull requests are merged with a **merge commit** — `main` does not accept
squash or rebase merges.

The `validate-examples` workflow runs on every pull request and verifies every example against the
JSON schema, asserts that the minimal valid fixtures under `tests/positive/` are accepted, and asserts that
the deliberately-invalid fixtures under `tests/negative/` are rejected.
It is meant to be a **required check**, so that a pull request cannot be merged until it passes; it runs
on every pull request but does not yet gate the merge. Treat a red run as blocking anyway.

Documentation lives in `docs/` and is built with mkdocs. No documentation site is published yet, for
the same reason: GitHub Pages is unavailable for a private repository on the Free plan.

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

Questions RFC-0037 has not settled are tracked as [GitHub issues](https://github.com/bitol-io/open-maturity-model-standard/issues).
