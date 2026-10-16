<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Open issues

Questions the standard has not settled, found by validating the only two maturity models that exist
against `schema/omms-json-schema-v0.2.0.json`. Both were surfaced rather than resolved: each changes
what OMMS permits, so the TSC should decide, not this repository.

Each entry records what was found, why it matters, the options, and what the schema does *meanwhile*.
The schemas currently accept every model that exists; nothing here blocks the repository.

---

## OMMS-001 — A `type: library` criterion may carry a metric and no threshold operator

**Found in:** ODCS model `L1-C02` (contract version present and SemVer-shaped) and `L1-C03` (status
holds a valid ODCS value); ODPS model `L1-P04` and `L2-P01` (status holds a valid ODPS value).

**The conflict.** RFC-0037's criterion Definitions table says of the threshold operator: *"Required
when `type` is `library`."* Four criteria in the two real models are `type: library`, carry a `metric`,
and name no operator at all:

```yaml
- id: L1-C02
  property: version
  type: library
  metric: validValues
  description: Contract version is present and follows SemVer format (any value).
```

Read literally, these four documents are invalid. Read for intent, they are presence checks: "the
field exists and holds a value of the right kind", which has no threshold to state. The metric alone
carries the meaning — `validValues` with nothing to compare against is an existence assertion.

**Why it matters.** This is the difference between a standard that describes maturity models and one
that describes only the subset of maturity models its authors had in mind. Existence checks are the
natural shape of Level 1 in both models, so this is not an edge case: it is what a structural level
looks like. An evaluator also needs an unambiguous rule — if the operator is absent, does the criterion
auto-pass, evaluate the metric against nothing, or is the document malformed?

**Options.**

1. **Operator optional** — a criterion may name a metric and no threshold; the evaluator treats the
   metric as an existence/validity check. Requires rewording RFC-0037's table so the requirement is
   "required when the criterion states a threshold at all".
2. **Operator required** — keep the RFC as written and change the four criteria: either give each a
   real threshold (they have none to give) or mark them `type: text`, which would auto-pass them and
   lose the machine evaluation that makes them useful at Level 1.
3. **Introduce an explicit operator** such as `mustBePresent` — honest about the intent, but adds
   vocabulary, which RFC-0037's Alternatives section went out of its way to avoid.

**Recommendation: option 1.** It is the only option that neither weakens the criteria nor grows the
vocabulary, and it matches what the models already mean. Option 2 is worse than it looks: converting
these to `type: text` would turn four real machine checks into auto-passes, quietly lowering the bar
at Level 1 in both models.

**Meanwhile:** the schema permits it — a criterion requires a `metric` (unless `type: text`) and never
requires an operator.

---

## OMMS-002 — `validValues` appears on a criterion, not only inside `arguments`

**Found in:** ODCS model `L1-C03`, `L1-C10`, `L3-C14` and `L5-C03`, where `validValues` sits directly
on the criterion:

```yaml
- id: L1-C10
  property: logicalType
  type: library
  metric: invalidValues
  validValues:
    - string
    - integer
```

RFC-0037 lists `validValues` as an **argument** — a key inside `arguments` — and the ODPS model does
it that way every time. So the two real models spell the same constraint two different ways:

```yaml
# ODCS L1-C10                    # ODPS L1-P04
metric: invalidValues            metric: validValues
validValues: [...]               arguments:
                                   validValues: [...]
```

**Why it matters.** A standard that accepts both spellings has no single answer to "where does a
value list go?", so tooling that writes maturity models cannot round-trip them, and a model author
reading the documentation gets no guidance. Worse, the two spellings are not even semantically
identical as written — one pairs the list with `invalidValues` (the list is the *permitted* set, so
anything outside it is invalid) and the other with `validValues` (the list is what is being counted).
That is a real difference in what the metric counts, and it is invisible until an evaluator picks one
reading.

**Options.**

1. **`arguments` only** — keep the RFC as written and correct the four ODCS criteria to match the
   ODPS model and the documentation.
2. **Both placements accepted** — tolerate the criterion-level spelling in the schema as an
   equivalent form.
3. **Both placements, defined semantics** — accept both and state in the standard which one an
   evaluator must honour when the two disagree (the criterion-level one, presumably).

**Recommendation: option 1.** The ODPS model and the RFC already agree; the ODCS model is the outlier,
and four criteria is a small correction. Option 2 buys compatibility at the price of two ways to say
one thing, which is exactly the sort of ambiguity a schema exists to remove. Option 3 is the worst of
the three: it makes the schema deliberately permissive *and* asks every implementer to get a
precedence rule right.

Note the metric pairing should be settled alongside the placement — `invalidValues` plus a list reads
as "values outside this list are invalid", which is a different assertion from `validValues` plus the
same list.

**Meanwhile:** the schema accepts `validValues` both on the criterion and inside `arguments`, so no
existing model is rejected. The criterion-level form is marked in the schema as tolerated for
compatibility and pointing at this entry.

---

## Resolved by copying the models unchanged

- The ODPS model's header cited **RFC-0036**. OMMS is **RFC-0037** (RFC-0036 is the archived
  environment-variables RFC). Corrected as the file was imported.
