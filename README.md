# BuildingDNA

**BuildingDNA** stands for **Building Data Network Archetype**. It is an open, machine readable data structure for building information that makes BIM data easier for software, automation, and AI systems to understand and use within the Architecture, Engineering, Construction, and Operations (AECO) domain.

BuildingDNA transforms information contained in BIMs into a consistent structure that combines semantic information about building elements with their geometry, properties, relationships, and traceable identifiers. It is not intended to create another BIM authoring format, but to provide a practical data layer that applications can reliably query and reason over.

The complete BuildingDNA specification is available in [`docs/BuildingDNA-Specification-v1.0.0.md`](docs/BuildingDNA-Specification-v1.0.0.md).

## BuildingDNA Dataset

A BuildingDNA dataset always consists of exactly three interconnected files:

| File             | Purpose                                                                   |
| ---------------- | ------------------------------------------------------------------------- |
| Semantic `.json` | Building elements, properties, parameters, identifiers, and relationships |
| `.map.json`      | Connection between semantic element identifiers and GLB geometry nodes    |
| `.glb`           | Renderable 3D geometry                                                    |

All three files together constitute the BuildingDNA dataset.

The semantic JSON is authoritative for semantic information, the GLB is authoritative for geometry, and the `.map.json` provides the connection between them.

## Repository Contents

### Documentation

The [`docs`](docs/) folder contains the formal BuildingDNA documentation:

* [`BuildingDNA-Specification-v1.0.0.md`](docs/BuildingDNA-Specification-v1.0.0.md) defines BuildingDNA v1.0.0 and its data structure, semantics, relationships, and rules.
* [`BuildingDNA-Versioning-v1.0.0.md`](docs/BuildingDNA-Versioning-v1.0.0.md) defines the BuildingDNA versioning approach.
* [`BuildingDNA-Query-Examples-v1.0.0.md`](docs/BuildingDNA-Query-Examples-v1.0.0.md) provides examples of querying BuildingDNA data.
* [`BuildingDNA-LICENSE`](docs/BuildingDNA-LICENSE) defines the licence applying to the BuildingDNA publication.

### JSON Schemas

The [`schemas`](schemas/) folder contains machine readable JSON Schemas for validating the JSON components of BuildingDNA v1.0.0:

* [`building-dna-semantic-v1.0.0.schema.json`](schemas/building-dna-semantic-v1.0.0.schema.json)
* [`building-dna-map-v1.0.0.schema.json`](schemas/building-dna-map-v1.0.0.schema.json)

The GLB file follows the glTF/GLB specification and therefore does not have a separate BuildingDNA JSON Schema.

### Examples

The [`examples`](examples/) folder contains example BuildingDNA datasets intended to demonstrate the structure and provide practical data for testing and exploration.

## Versioning

BuildingDNA uses semantic versioning:

`MAJOR.MINOR.PATCH`

The current specification is **BuildingDNA v1.0.0**.

The semantic JSON and `.map.json` files contain a `buildingDnaVersion` field identifying the BuildingDNA version used by the dataset.

See [`docs/BuildingDNA-Versioning-v1.0.0.md`](docs/BuildingDNA-Versioning-v1.0.0.md) for the complete versioning rules.

## Extensibility

BuildingDNA defines a stable core structure while remaining extensible as new BIM use cases emerge.

Additional semantic fields, relationships, and categories may be introduced through future BuildingDNA versions while preserving the versioning rules defined for the project.

BuildingDNA is intended to remain independent of any particular BIM authoring platform, application, or measurement system.

## Licensing

Copyright © 2026 Pyramoon Innovations Inc.

BuildingDNA is published under the **Creative Commons Attribution NoDerivatives 4.0 International (CC BY ND 4.0)** licence.

The licence applies to the BuildingDNA specification, schemas, documentation, query examples, and published example datasets.

See [`docs/BuildingDNA-LICENSE`](docs/BuildingDNA-LICENSE) for the applicable terms.

## Initiative

BuildingDNA is initiated by **Pyramoon Innovations Inc.** and supported by **BIMbc** as part of a broader effort to advance practical digital transformation and AI readiness in the building industry.

More information is available at **BuildingDNA.ca**.
