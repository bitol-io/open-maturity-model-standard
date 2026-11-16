<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# OMMS JSON Schema

You will find all the versions of the Open Maturity Model Standard (OMMS) JSON Schema files here. Each
version will be stored here for reference and to allow
[SchemaStore](https://github.com/SchemaStore/schemastore) to keep track of each version.

If you want your files to be automatically detected as OMMS files, use the file extension
`.omms.yaml`. For example, `odcs-maturity-model.omms.yaml`.

## SchemaStore

SchemaStore is a widely adopted repository of JSON schema files for integration with tools such as
Intellij and VS Code. OMMS is not yet onboarded to SchemaStore; a pull request should be opened
(mirroring the [ODCS](https://github.com/SchemaStore/schemastore) and
[ODPS](https://github.com/SchemaStore/schemastore/pull/3868) registrations) once OMMS reaches a public
release.

## Versioning

When a new version of OMMS JSON Schema is available, a pull request to SchemaStore should be created to
update the following section:

```json
    {
      "name": "Open Maturity Model Standard (OMMS)",
      ...,
      "versions": {
        "<new_version>": "https://github.com/bitol-io/open-maturity-model-standard/blob/main/schema/omms-json-schema-<new_version>.json",
        "v0.2.2": "https://github.com/bitol-io/open-maturity-model-standard/blob/main/schema/omms-json-schema-v0.2.2.json",
        "v0.2.1": "https://github.com/bitol-io/open-maturity-model-standard/blob/main/schema/omms-json-schema-v0.2.1.json",
        "v0.2.0": "https://github.com/bitol-io/open-maturity-model-standard/blob/main/schema/omms-json-schema-v0.2.0.json"
      }
    },
```

And the `omms-json-schema-latest.json` should be updated with the latest version changes. The `latest`
file is a copy of the newest versioned file with only its `$id` rewritten, the same convention the
OORS schema directory uses.

## Validating

```bash
bash scripts/validate-examples.sh
```

The script compiles the schema, validates every example under `docs/examples/`, asserts that every
fixture under `tests/positive/` is accepted, and that every fixture under `tests/negative/` is rejected. Set `JSON_SCHEMA_VERSION` to validate against a different
version, e.g. `JSON_SCHEMA_VERSION=v0.2.0`. The default is the newest version.
