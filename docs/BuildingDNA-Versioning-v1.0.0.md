# BuildingDNA Versioning

Copyright © Pyramoon Innovations Inc. Licensed under CC BY-ND 4.0. See
[`BuildingDNA-LICENSE`](BuildingDNA-LICENSE).

## Version format

`MAJOR.MINOR.PATCH`

## MAJOR

Increment when an existing compliant consumer may no longer correctly interpret the data.

Examples:

* removing a field
* renaming a field
* changing a field's meaning
* changing a field's JSON type
* changing an existing structural relationship incompatibly

## MINOR

Increment for backward-compatible additions.

Examples:

* adding a new optional field
* adding a new optional semantic capability that does not alter existing field meaning

## PATCH

Increment when the BuildingDNA data contract does not change.

Examples:

* specification wording correction
* schema correction that brings the schema into alignment with an already-established contract
* documentation correction

Do not use PATCH for an actual serialized-data change.

## Where the version appears

* Every BuildingDNA semantic JSON contains `buildingDnaVersion`.
* Every BuildingDNA `.map.json` contains the same `buildingDnaVersion`.
* A consumer must inspect the version before assuming compatibility.
* GLB does not duplicate the BuildingDNA version. BuildingDNA version compatibility does not redefine glTF/GLB versioning.
