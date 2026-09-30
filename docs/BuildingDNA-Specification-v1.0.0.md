# BuildingDNA v1.0.0 Specification

Version 1.0.0. See [`BuildingDNA-Versioning-v1.0.0.md`](BuildingDNA-Versioning-v1.0.0.md) for the meaning of each version component.

Copyright © Pyramoon Innovations Inc. Licensed under CC BY-ND 4.0 - see
[`BuildingDNA-LICENSE`](BuildingDNA-LICENSE).

## 1. Purpose and Principles

BuildingDNA stands for Building Data Network Archetype. It is an initiative to create an open, machine readable data structure for building information that makes BIM data easier for software, automation, and AI systems to understand and use within the Architecture, Engineering, Construction, and  Operations (AECO) domain.

BuildingDNA transforms information contained in Building Information Models (BIMs) into a consistent structure that combines semantic information about building elements with their geometry, properties, relationships, and traceable identifiers. In this way, BuildingDNA provides a representation of a building's elements, categorization, parameters, and relationships, extracted from a source BIM authoring model and packaged for consumption independent of that authoring tool.
This initiative is not intended to create another BIM authoring format, but to provide a practical data layer that applications can reliably query and reason over.

BuildingDNA is intended to enable downstream applications across design, construction, operations, compliance checking, analytics, artificial intelligence, visualization, and other workflows.
It aims to provide a reusable foundation for applications such as automated building code and regulatory checking, design quality assurance, model auditing, information validation, energy and sustainability workflows, digital permitting, facility management, and AI enabled applications. Its purpose is to reduce the need for each application to develop its own method of extracting and interpreting BIM data.

The objective is to encourage AECO applications to consume BIM-based data through BuildingDNA rather than depend directly on proprietary BIM authoring tools. A BuildingDNA dataset is intended to contain the building information required by downstream applications and make that dataset openly accessible through the BuildingDNA structure, so that the application does not need to open the original authoring file or depend on the authoring tool's API.

## 2. BuildingDNA Dataset

A BuildingDNA dataset is distributed as an interconnected bundle consisting of three files:

| File role        | Role                                                                                 |
| ---------------- | ------------------------------------------------------------------------------------ |
| Semantic `.json` | Contains element identity, categorization, parameters, properties, and relationships |
| `.map.json`      | Maps semantic element identifiers to geometry node indices                           |
| `.glb`           | Contains the corresponding renderable 3D geometry                                    |
BuildingDNA refers to the complete bundle rather than any individual file. It is an interconnected dataset containing exactly three files: a semantic JSON, a map JSON, and a GLB geometry file that together can represent the required BIM data for an intended purpose. The semantic JSON provides the building information and relationships, while the GLB provides geometry. The `.map.json` connects the two by associating each semantic element identifier with its corresponding geometry nodes.

A BIM dataset fundamentally consists of both semantic information and geometric representation, and removing either component means the dataset no longer constitutes a complete BIM representation. BuildingDNA follows this principle by requiring all three files as one interconnected dataset. Therefore, neither the semantic JSON nor the GLB geometry file alone constitutes BuildingDNA. The semantic JSON is authoritative for the semantic and non-geometric BIM information, while the GLB is authoritative for the geometric representation. The `.map.json` connects the two by associating semantic element identifiers with corresponding GLB geometry nodes, allowing users and applications to retrieve semantic records from geometry or identify geometry from semantic information.

Every semantic JSON and `.map.json` file in a BuildingDNA dataset carries a `buildingDnaVersion` field. These two files must report the same `buildingDnaVersion`, and a consumer must inspect that version before assuming compatibility. The GLB file does not carry this field because BuildingDNA versioning does not redefine glTF/GLB versioning. See [`BuildingDNA-Versioning-v1.0.0.md`](BuildingDNA-Versioning-v1.0.0.md) for the version format and the meaning of each version component.

## 3. Identifiers, References, and Geometry Mapping

### 3.1 Element Identifiers

Every semantic record in BuildingDNA carries two identifiers:

| Identifier | Persistence                                          | Used for references | Purpose                                                                                                      |
| ---------- | ---------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------ |
| `uniqueId` | Persistent across exports of the same source element | Yes                 | Identifies the element and connects it to other records and geometry                                         |
| `intId`    | Not guaranteed to remain stable across exports       | No                  | Provides a numeric identifier for traceability, logging, debugging, and comparison with the source BIM |

`uniqueId` is the main identifier used by BuildingDNA. It must be unique within the dataset and is used whenever one element needs to reference another element.

`intId` is provided as additional source model information. It must not be used to create relationships between BuildingDNA records.

### 3.2 Referencing Elements

BuildingDNA uses `uniqueId` values to describe relationships between elements. For example, `typeId`, `hostId`, `parentId`, and `levelId` can connect an element to its type, host, parent element, or associated level.

When a reference contains an ID, that ID may point to an element that is present in the same BuildingDNA dataset or to an element that exists in the source BIM but was not included in the dataset. The definition of each reference must state whether the referenced element is required to be present in the same dataset.

BuildingDNA can introduce additional references when particular use cases require them. For example, room related references such as `roomId`, `fromRoomId`, and `toRoomId` can be used when spatial relationships are required. These references follow the same principle: they use the `uniqueId` of the referenced element.

This allows the referencing structure to grow over time without changing the basic way BuildingDNA connects elements.

### 3.3 Semantic and Geometry Mapping

BuildingDNA uses the `.map.json` file to connect semantic records in the semantic JSON with geometry nodes in the GLB file.

The mapping uses an element's `uniqueId` and the corresponding GLB node indices. One semantic element may be represented by one or more geometry nodes.

The mapping can be used in both directions. A consumer can start with a semantic record and find its geometry, or start with a geometry node and find the corresponding semantic record.

The semantic JSON and GLB therefore use different identifiers for the same building elements, and `.map.json` provides the connection between them.

## 4. Semantic JSON Structure

The semantic JSON contains the non geometric information in a BuildingDNA dataset. It organizes building elements, their types, parameters, relationships, and building level information into a consistent machine readable structure.

### 4.1 Root Structure

The semantic JSON contains the following top level fields:

| Field                | Purpose                                                                          |
| -------------------- | -------------------------------------------------------------------------------- |
| `buildingDnaVersion` | Identifies the BuildingDNA version used by the dataset                           |
| `types`              | Contains element type records                                                    |
| `levels`             | Contains Level records                                                           |
| `rooms`              | Contains Room records                                                            |
| `spaces`             | Contains MEP Space records                                                       |
| `doors`              | Contains Door records                                                            |
| `walls`              | Contains Wall records                                                            |
| `windows`            | Contains Window records                                                          |
| `other`              | Contains exported elements that do not belong to the dedicated collections above |
| `siteContext`        | Contains building site related information                                       |
| `buildingContext`    | Contains information that applies to the building as a whole                     |

The element collections `types`, `levels`, `rooms`, `spaces`, `doors`, `walls`, `windows`, and `other` use the following structure:

```json
{
  "items": []
}
```

A BuildingDNA semantic JSON therefore follows this overall structure:

```json
{
  "buildingDnaVersion": "1.0.0",
  "types": { "items": [] },
  "levels": { "items": [] },
  "rooms": { "items": [] },
  "spaces": { "items": [] },
  "doors": { "items": [] },
  "walls": { "items": [] },
  "windows": { "items": [] },
  "other": { "items": [] },
  "siteContext": {},
  "buildingContext": {}
}
```

### 4.2 Element Records and Types

BuildingDNA distinguishes between element instances and element types.

An instance record represents an individual element in the building, such as a particular wall, door, or window. A type record represents information shared by elements of the same type.

An instance can reference its type through `typeId`. The corresponding type information is stored once in the `types` collection rather than being repeated in every instance.

A type record contains its own identifiers, category, family, type name, and parameters.

### 4.3 Element Collections

Element instances are organized into collections according to their source BIM category:

| Source element          | BuildingDNA collection |
| ----------------------- | ---------------------- |
| Level                   | `levels`               |
| Room                    | `rooms`                |
| MEP Space               | `spaces`               |
| Door                    | `doors`                |
| Wall                    | `walls`                |
| Window                  | `windows`              |
| Other exported elements | `other`                |

Elements placed in `other` retain their source category so that consumers can identify what they represent.

The dedicated collections provide a consistent location for commonly used building elements while allowing BuildingDNA to include additional categories without requiring a separate top level collection for every possible BIM category.

### 4.4 Parameters

Every element instance and type record contains a `parameters` object representing parameter values available from the source BIM.

Parameter names are not predefined by BuildingDNA. They reflect the parameters available on the corresponding source element.

Parameter values are stored as strings. Parameters that have no value in the source BIM are omitted rather than stored as `null`.

For Length, Area, and Volume parameters, BuildingDNA stores the value using a consistent representation that includes the value and its unit. For example:

* Length: `<value> ft`
* Area: `<value> ft²`
* Volume: `<value> ft³`

The unit may follow any measurement system supported by the source BIM. Other parameters retain the formatted display value provided by the source BIM.

### 4.5 Levels

Levels are stored in the `levels` collection.

In addition to the common element information, each Level record contains:

`countsAsBuildingStorey`

This boolean field indicates whether the Level is classified as a building storey.

### 4.6 Building Level Information

Information that applies to the building as a whole rather than to an individual element is stored separately from element collections.

`buildingContext` contains:

| Field                           | Meaning                                              |
| ------------------------------- | ---------------------------------------------------- |
| `buildingArea`                  | Human readable building area                         |
| `buildingAreaValue`             | Numeric building area                                |
| `buildingAreaUnit`              | Unit used by `buildingAreaValue`                     |
| `buildingAreaSource`            | Indicates how the building area was established      |
| `majorOccupancyClassifications` | Occupancy classifications applicable to the building |

`buildingContext` is always present. If unavailable, `buildingArea`, `buildingAreaValue`, `buildingAreaUnit`, and `buildingAreaSource` are `null`, while `majorOccupancyClassifications` is an empty array `[]`.

`siteContext` is also stored at the root level and is defined separately in the Site Context section. 

## 5. Element Relationships

BuildingDNA represents relationships between building elements through reference fields. As defined in Section 3, these fields use the referenced element's `uniqueId`.

### 5.1 Core Element Relationships

The following relationships describe common connections between building elements:

| Field      | Relationship       | Meaning                                                        |
| ---------- | ------------------ | -------------------------------------------------------------- |
| `typeId`   | instance to type   | Identifies the type record associated with an element instance |
| `hostId`   | instance to host   | Identifies the element that hosts the instance                 |
| `parentId` | instance to parent | Identifies the parent element of a nested or component element |
| `levelId`  | instance to level  | Identifies the level associated with the element               |

These fields are included when the corresponding relationship exists in the source BIM information.

### 5.2 Room Relationships

BuildingDNA may include additional relationships required for particular BIM use cases. Room relationships are one such extension.

| Field                 | Relationship               | Meaning                                                                   |
| --------------------- | -------------------------- | ------------------------------------------------------------------------- |
| `roomId`              | instance to room           | Identifies the room containing the element                                |
| `fromRoomId`          | door or window to room     | Identifies the room on the from side of the element                       |
| `toRoomId`            | door or window to room     | Identifies the room on the to side of the element                         |
| `roomRelationPhaseId` | room relationship to phase | Identifies the construction phase used to determine the room relationship |

Doors and Windows use `fromRoomId` and `toRoomId` because they may connect two different rooms. Other applicable element instances may use `roomId` to identify the room containing them.

Any of these room references may be `null` when the corresponding room cannot be identified or the relationship does not exist.

### 5.3 Room Surrounding Element Relationships

A Room may also identify the building elements that form its physical boundaries. BuildingDNA currently supports the following surrounding element collections:

* `surroundingWalls`
* `surroundingFloors`
* `surroundingCeilings`
* `surroundingRoofs`

Each entry identifies the surrounding element by `uniqueId` and may also contain information describing the shared boundary area.

These relationships are based on spatial and boundary information available from the source BIM rather than geometric proximity alone.

## 6. Room and Spatial Semantics

BuildingDNA includes spatial information that describes Rooms and their relationships to the elements that define or interact with them.

### 6.1 Room Area

A Room can have different area values depending on the boundary method used for measurement. BuildingDNA records the available area values for the following boundary methods:

| Method         | Meaning                                           |
| -------------- | ------------------------------------------------- |
| `Finish`       | Area measured to interior finish faces            |
| `Center`       | Area measured to wall centre lines                |
| `CoreBoundary` | Area measured to the boundary of the wall core    |
| `CoreCenter`   | Area measured to the centre line of the wall core |

These values are stored under `roomAreas`:

```json
"roomAreas": {
  "activeMethod": "Center",
  "unit": "Square feet",
  "values": {
    "Finish": 118.2,
    "Center": 120.0,
    "CoreBoundary": null,
    "CoreCenter": 119.1
  }
}
```

`activeMethod` identifies the boundary method used by the source BIM for the Room's reported area. `unit` identifies the unit used by all numeric values in `values`.

If an area cannot be determined for a particular boundary method, that value is `null` without affecting the other available values.

Each Room also contains `isEnclosed`, which indicates whether the source BIM can determine a closed spatial boundary for that Room.

### 6.2 Surrounding Elements

A Room may contain references to the elements that form its physical boundaries through the following fields:

| Field                 | Surrounding element type |
| --------------------- | ------------------------ |
| `surroundingWalls`    | Walls                    |
| `surroundingFloors`   | Floors                   |
| `surroundingCeilings` | Ceilings                 |
| `surroundingRoofs`    | Roofs                    |

Each entry identifies the surrounding element and may include the area of the surface shared with the Room:

```json
{
  "uniqueId": "...",
  "facingArea": "26 SF",
  "facingAreaValue": 26.34,
  "facingAreaUnit": "Square feet"
}
```

If the same element borders a Room through multiple surfaces, those surfaces are combined into a single entry and their measurable facing areas are added together.

Room surrounding element relationships and facing areas are determined from the spatial boundary information available in the source BIM rather than from geometric proximity.

These relationships and facing areas use the `Finish` boundary method independently of `roomAreas.activeMethod`. Changing the Room area boundary method therefore does not change the surrounding element relationships or their facing areas.

## 7. Site Context

`siteContext` contains information that describes the relationship between the building and its site.

BuildingDNA currently uses the following fields:

| Field                           | Meaning                                                                |
| ------------------------------- | ---------------------------------------------------------------------- |
| `finishedGradeReferenceId`      | `uniqueId` of the element representing finished grade                  |
| `finishedGradeReferenceType`    | Type of element used to represent finished grade                       |
| `finishedGradeReferencePhaseId` | Construction phase in which the finished grade reference was evaluated |

When a finished grade reference is available, these fields identify the source BIM element used to represent it.

If no single finished grade reference can be established, all three fields are `null`:

```json
"siteContext": {
  "finishedGradeReferenceId": null,
  "finishedGradeReferenceType": null,
  "finishedGradeReferencePhaseId": null
}
```

BuildingDNA does not infer finished grade from element names or assumed elevation values. The finished grade reference must come from information available in the source BIM.


## 8. Geometry Mapping

The `.map.json` file defines the connection between the semantic JSON and the GLB geometry file.

The file contains the BuildingDNA version and a mapping from each semantic element's `uniqueId` to the corresponding GLB node indices:

```json
{
  "buildingDnaVersion": "1.0.0",
  "<uniqueId-1>": [12, 13],
  "<uniqueId-2>": [27]
}
```

A single semantic element may correspond to more than one GLB node. All node indices associated with that element are therefore stored in the same array.

An element that has a semantic record but no renderable geometry does not require an entry in `.map.json`.

Although the file stores the mapping as `uniqueId` to GLB node indices, the same information can be used in either direction: from a semantic element to its geometry, or from a geometry node to its corresponding semantic element.

The semantic JSON remains authoritative for semantic information, while the GLB remains authoritative for geometry. The `.map.json` provides the connection between them.

## 9. Units and Values

BuildingDNA is designed to remain independent of any particular measurement system or source model unit configuration. The data structure does not require a building to use imperial, metric, or any other specific unit system.

BuildingDNA distinguishes between human readable display values and precise numeric values intended for computation:

| Value type              | Purpose                                                                    |
| ----------------------- | -------------------------------------------------------------------------- |
| Formatted display value | Human readable value intended for display and review                       |
| Precise numeric value   | Numeric value intended for computation and accompanied by an explicit unit |

Where a precise numeric quantity is provided, its unit is stated explicitly rather than assumed from the source BIM or application environment. This allows the same BuildingDNA structure to represent equivalent information using different unit systems.

For example:

```json
{
  "facingArea": "26 SF",
  "facingAreaValue": 26.34,
  "facingAreaUnit": "Square feet"
}
```

The same structure could represent an area in another unit system:

```json
{
  "facingArea": "2.45 m²",
  "facingAreaValue": 2.45,
  "facingAreaUnit": "Square metres"
}
```

The field structure remains unchanged; only the value and its declared unit differ. This allows BuildingDNA datasets originating from different modelling environments, regions, and unit conventions to follow the same data structure.

When both a formatted display value and a precise numeric value are provided for the same quantity, applications should use the precise numeric value together with its explicit unit for calculations rather than attempting to extract a number from the formatted display value.

This separation between data structure and unit representation is intended to keep BuildingDNA flexible and suitable for different BIM authoring platforms, project standards, and regional measurement systems.


## 10. Null, Empty, and Unavailable Values

BuildingDNA distinguishes between information that is absent, unavailable, empty, or explicitly zero:

| State        | Meaning                                                               |
| ------------ | --------------------------------------------------------------------- |
| Field absent | The information does not apply to this record                         |
| `null`       | The information applies but could not be determined or is unavailable |
| `""`         | The source BIM contains an explicit blank value                       |
| `[]`         | The collection or relationship was evaluated and no items were found  |
| `0`          | The value was explicitly determined to be zero                        |

`null` must not be interpreted as zero, and a valid value of zero must not be interpreted as unavailable.

For Room surrounding element arrays, `null` means that the relationship could not be determined or was unavailable, while `[]` means that the relationship was evaluated successfully and no surrounding elements of that category were found.

A surrounding element relationship may remain valid even when its `facingArea` or `facingAreaValue` is `null`. In that case, the relationship to the surrounding element is known, but the shared area is unavailable. Any calculation that depends on all facing areas must therefore be treated as incomplete when one or more required values are `null`.

## 11. Authoritative and Inferred Information

BuildingDNA represents information that exists in the source BIM or can be determined directly from relationships and data available within that model.

BuildingDNA should not assign architectural meaning that is not supported by the source BIM information. For example, a space should only be represented as a Room when the source BIM contains a corresponding Room object. BuildingDNA must not classify an unenclosed or partially enclosed space as a balcony, corridor, or any other space type based only on its geometry.

Similarly, fields such as `isEnclosed` describe a specific property of the source BIM object and should not be interpreted as implying another classification or use.

Where BuildingDNA introduces derived information, the method used to derive that information should be explicitly defined so that consumers can distinguish source information from information calculated through BuildingDNA logic.

## 12. Extensibility

BuildingDNA is intended to provide a stable core structure while remaining flexible enough to support additional BIM information and future use cases.

New semantic fields, relationships, and categories may be introduced as new requirements emerge, provided that they follow the existing BuildingDNA principles and versioning rules.

Application specific information derived from BuildingDNA should remain separate from the core BuildingDNA dataset unless it is formally introduced into the BuildingDNA specification.

BuildingDNA is also intended to support information originating from different BIM authoring platforms rather than being tied to one particular software environment.

## 13. BuildingDNA v1.0.0 Scope and Limitations

BuildingDNA v1.0.0 defines the initial public structure for representing BIM information through an interconnected semantic JSON, map JSON, and GLB geometry file.

The current version does not attempt to represent every type of BIM information or every possible relationship found in BIM authoring platforms. Additional fields, relationships, and semantic capabilities may be introduced in future versions as new use cases are identified.

BuildingDNA v1.0.0 currently focuses on building element information, parameters, element relationships, spatial information, building and site context, and the connection between semantic information and geometry.

The specification defines the BuildingDNA data structure independently of any particular exporter or BIM authoring platform. Software used to produce BuildingDNA datasets may have its own implementation limitations, which are not limitations of the BuildingDNA specification itself.
