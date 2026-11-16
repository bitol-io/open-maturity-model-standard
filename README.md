---
title: "Open Maturity Model Standard (OMMS)"
description: "Home of Open Maturity Model Standard (OMMS) documentation."
image: "https://raw.githubusercontent.com/bitol-io/artwork/main/horizontal/color/Bitol_Logo_color.svg"
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Open Maturity Model Standard (OMMS)

Welcome!

Thanks for your interest and for taking the time to come here! ❤️

> ## WORK IN PROGRESS — NOT READY FOR PRODUCTION
>
> OMMS is a **draft standard**, circulated within the Bitol TSC so it can be shaped before release. It is
> **not** a released standard. Expect **breaking changes in every version until v1.0.0**, and incomplete
> areas. Do not build production systems against it.

## Executive summary

This standard describes a structure for a **maturity model**: the yardstick that says how complete,
governed and operational a Bitol artifact, such as an [ODCS](https://github.com/bitol-io/open-data-contract-standard)
data contract or an [ODPS](https://github.com/bitol-io/open-data-product-standard) data product, is.
Its current version is v0.2.0. It is available under the Apache 2.0 license. Contributions are welcome!

## Discover the open standard

Discover the [Open Maturity Model Standard](docs/README.md). This page explains what the standard is,
how it differs from ODCS and ODPS, and gives an example. More [examples](docs/examples/README.md),
including the real ODCS and ODPS maturity models, can be found here.

No documentation site is published yet: OMMS is a private repository on a GitHub Free organisation,
where Pages is unavailable.

### JSON Schema

The JSON Schema for OMMS is in [`schema/`](https://github.com/bitol-io/open-maturity-model-standard/tree/main/schema).
Files using the `.omms.yaml` extension are auto-detected as OMMS.

## Known open issues

Questions the standard has not settled yet are recorded, with options and a recommendation, in
[`issues.md`](https://github.com/bitol-io/open-maturity-model-standard/blob/main/issues.md).

## Articles and Other Resources

Check out the [resources](resources.md) page.

## Contributing to the project

Check out the [CONTRIBUTING](CONTRIBUTING.md) page. OMMS is defined by
[RFC-0037](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed with the Bitol TSC and
not yet approved.

## License

This project is licensed under the Apache License 2.0 — see [LICENSE](https://github.com/bitol-io/open-maturity-model-standard/blob/main/LICENSE).

## More

### Citation

If you need to cite this standard, you can use the following BibTeX entry:

```bibtex
@manual{OMMS2026,
  title        = {Open Maturity Model Standard (OMMS)},
  author       = {{Bitol}},
  organization = {LF AI \& Data Foundation},
  year         = {2026},
  url          = {https://github.com/bitol-io/open-maturity-model-standard}
}
```

### Mime/Media Type

The official media (fka mime) type is:

```
application/omms+yaml;version=0.2.0
```
