---
title: "Custom &amp; Other Properties"
description: "Extending OMMS with customProperties and authoritativeDefinitions."
---

<!--
Copyright 2026 The Bitol Contributors
SPDX-License-Identifier: Apache-2.0
-->

# Custom & Other Properties

OMMS carries two shared Bitol structures for the things a standard cannot anticipate: `customProperties`
for vendor- and organization-specific extensions, and `authoritativeDefinitions` for links to sources
that explain the model.

Both are the same structures ODCS, ODPS and OORS use, so a tool that reads one reads them all.

[Back to TOC](README.md)

## Example

### Minimal

A single custom property.

```yaml
customProperties:
  - property: exampleOf
    value: OMMS v0.2.2
```

### Full

Both structures, with their optional fields populated.

```yaml
customProperties:
  - property: dataQoSReference
    value: "Perrin, 2023"
    description: >
      Data QoS dimensions (Accuracy, Completeness, Conformity, Consistency,
      Coverage, Timeliness, Uniqueness) per Jean-Georges Perrin, 2023.

authoritativeDefinitions:
  - type: canonicalUrl
    url: https://github.com/bitol-io/open-data-contract-standard
    description: ODCS repository — the standard this model evaluates against.
  - type: businessDefinition
    url: https://dataintelligenceplatform.substack.com/p/five-levels-between-chaos-and-almost
    description: >
      "Five Levels Between Chaos and (Almost) AI-Ready Data" by
      Jean-Georges Perrin, 2026.
```

## Where they may appear

`customProperties` and `authoritativeDefinitions` may appear at any level of an OMMS document:

| Location  | Notes                                                              |
| --------- | ------------------------------------------------------------------ |
| Model     | Top level, alongside `kind` and `levels`.                          |
| `policies[]` | On an individual policy.                                        |
| `levels[]`   | On an individual level.                                         |
| `criteria[]` | On an individual criterion.                                     |

## Custom properties

A list of key/value pairs for extensions specific to a vendor, an organization, or a one-off need.

### Definitions

| Key                     | Type   | UX label           | Required | Description                                                                                                                                                                                                |
| ----------------------- | ------ | ------------------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `customProperties`      | array  | Custom Properties  | No       | A list of key/value pairs for custom properties.                                                                                                                                                            |
| `customProperties[].property` | string | Property     | Yes      | The name of the key. Names should be in camel case—the same as if they were permanent properties in the standard.                                                                                          |
| `customProperties[].value`    | any    | Value        | Yes      | The value of the key.                                                                                                                                                                                       |
| `customProperties[].description` | string | Description | No     | Optional description of the custom property.                                                                                                                                                                |
| `customProperties[].id`       | string | ID           | No       | Optional unique identifier for the custom property, for stable references.                                                                                                                                  |
| `customProperties[].vendor`   | string | Vendor       | No       | Identifies the vendor, provider, or external system associated with this custom property. SHOULD be a stable, lowercase identifier matching `^[a-z0-9][a-z0-9-]*$`. Tools MUST preserve unknown vendor values. |

### Recommendations

- **Prefix the name when the property is not yours.** A bare `owner` competes with every other tool's
  idea of an owner; `acme.owner` does not. Prefixes are a convention, not a schema rule.
- **Use `vendor` rather than encoding the vendor in the property name.** It is what the field is for,
  and it lets a tool filter which extensions it understands without parsing strings.
- **Use `id` when something needs to reference the property.** A `customProperties` entry with an `id`
  can be pointed at; one without cannot.

## Authoritative definitions

A list of links to sources that provide more details on the model: a canonical URL for the model itself,
the article that inspired its level names, the repository of the standard it evaluates.

### Definitions

| Key                             | Type   | UX label            | Required | Description                                                                                                                                                    |
| ------------------------------- | ------ | ------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `authoritativeDefinitions`      | array  | Authoritative Defs  | No       | A list of links to sources that provide more details.                                                                                                          |
| `authoritativeDefinitions[].url` | string | URL                | Yes      | URL to the authority.                                                                                                                                          |
| `authoritativeDefinitions[].type` | string | Type              | Yes      | Type of definition for authority. See the recommended values below.                                                                                             |
| `authoritativeDefinitions[].description` | string | Description | No  | Description of the authoritative definition for humans.                                                                                                         |
| `authoritativeDefinitions[].id`  | string | ID                  | No       | Optional unique identifier for the definition, for stable references.                                                                                           |

Recommended values for `type`, shared with the other Bitol standards:

| Value                         | For                                                  |
| ----------------------------- | ---------------------------------------------------- |
| `canonicalUrl`                | The canonical home of the model or the standard.     |
| `businessDefinition`          | A definition of a business concept the model uses.   |
| `glossary`                    | A glossary entry.                                    |
| `implementation`              | An implementation of the standard or the model.      |
| `ontology`                    | An ontology.                                         |
| `taxonomy`                    | A taxonomy.                                          |
| `transformationImplementation` | A transformation that implements something.         |
| `tutorial`                    | A tutorial.                                          |
| `videoTutorial`               | A video tutorial.                                    |

[Back to TOC](README.md)
