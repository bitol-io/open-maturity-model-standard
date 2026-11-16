# History

## Version 0.2.x
OMMS becomes the formal consequence of [RFC-0037](https://github.com/bitol-io/tsc/blob/main/rfcs/0037-omms.md), filed with the Bitol TSC and not yet approved. This is the first version of the standard to exist outside the RFC: a JSON Schema, documentation, examples and validation CI.

The standard defines a YAML syntax for maturity models that evaluate Bitol artifacts — data contracts (ODCS) and data products (ODPS), and potentially others. Its criteria started from the ODCS data quality vocabulary and, from v0.2.1, use two metrics of their own (`valueOf` and `count`); it introduces `policies` as a first-class concept for the organizational rules that cap or constrain an evaluation.

Two maturity models ship with it — one for ODCS contracts and one for ODPS products — both at v2.1.0, with five levels each and **Level 4 as the AI-Ready level**: the point at which a machine consumer, a quality engine or an AI agent, can test the artifact and use it safely. They are the only maturity models in existence; before this release they lived as private seeds inside the Bitol Workbench.

OMMS v0.2.1 is currently in development.

## Origins
OMMS was created within [Bitol](https://bitol.io), part of the [Linux Foundation AI & Data](https://lfaidata.foundation/), alongside its sibling standards ODCS (Open Data Contract Standard), ODPS (Open Data Product Standard) and OORS (Open Observability Results Standard). It follows the same lightweight, human-readable philosophy, and it is the standard that lets the others be scored rather than only written: the maturity models in this repository evaluate contracts and products the way OORS reports on them.

The five-level progression the shipped models use — Structural, Descriptive, Governed, AI-Ready, Optimized — comes from "Five Levels Between Chaos and (Almost) AI-Ready Data" by Jean-Georges Perrin (2026). Earlier experimental models, built for ODCS before the standard existed, informed the design: what they called `rule` became `metric`, their `scope` field was removed in favour of `metric` plus `arguments`, and their units (`items`, `errors`, `members`, `dimensions`) collapsed into `rows`.
