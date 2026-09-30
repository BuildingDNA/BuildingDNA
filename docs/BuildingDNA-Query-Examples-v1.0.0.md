# 

# BuildingDNA 

# Query Examples

## 

## by  Pyramoon Innovations October 2026

## **Introduction**

The following examples use LIQ JSON queries executed by the current Query Execution Engine (QEE) to demonstrate how BuildingDNA data can be queried. LIQ and QEE are not part of the BuildingDNA specification; they are used here only as one practical method for accessing and testing BuildingDNA data.

## **1\. Basic element lookup**

Retrieve a specific element using a known identifier such as `uniqueId` or `intId`. This demonstrates direct access to one semantic record.

Question: Retrieve the element with intId 406391\.

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "one",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "intId",
            "op": "eq",
            "value": 406391
          }
        ]
      }
    }
  ],
  "return": {
    "element": "$step1"
  }
}
```

Expected result: One complete Door instance record with intId 406391

Actual result:

```json
{
  "element": {
    "category": "Doors",
    "fromRoomId": "e231fe63-021b-4ca8-9bdc-bc11c95cc525-00071454",
    "hostId": "8d2f680f-034d-4a68-9767-ba166369672f-00062f87",
    "intId": 406391,
    "levelId": "26bea7fc-fb11-4ce2-99e8-8b3ac671ef08-0004d872",
    "parameters": {
      "Area": "28.5187 ft²",
      "Category": "Doors",
      "Design Option": "-1",
      "Export to IFC": "By Type",
      "Family": "Doors_IntSgl",
      "Family and Type": "Doors_IntSgl: 910x2110mm",
      "Head Height": "6.9226 ft",
      "Host Id": "405383",
      "IfcGUID": "2DBsWF0qrAQ9TdkXPZRrHO",
      "Level": "Level 2",
      "Mark": "3",
      "Phase Created": "New Construction",
      "Phase Demolished": "None",
      "Sill Height": "0 ft",
      "Type": "910x2110mm",
      "Type Id": "177722",
      "Volume": "3.1751 ft³"
    },
    "parentId": null,
    "roomRelationPhaseId": "e3e052f9-0156-11d5-9301-0000863f27ad-00000000",
    "toRoomId": "e231fe63-021b-4ca8-9bdc-bc11c95cc525-000713b7",
    "typeId": "0c88ab75-a0bb-4c49-9f21-70e32c6d4d90-0002b63a",
    "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063377"
  }
}
```

## **2\. Counting elements**

Count how many elements of a particular category exist, such as Doors, Walls, Rooms, or Windows. This demonstrates simple aggregation over BuildingDNA collections.

Question: How many Door instances are in the model, excluding Door type records?

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "many",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Doors"
          },
          {
            "field": "typeId",
            "op": "exists",
            "value": true
          }
        ]
      }
    },
    {
      "id": "step2",
      "op": "calculate",
      "expression": {
        "operation": "aggregate",
        "function": "count",
        "of": "$step1"
      }
    }
  ],
  "return": {
    "value": "$step2"
  }
}
```

Expected result: One integer under "value" representing the total number of Door instances.

Actual results:

```json
{
  "value": 14
}
```

## **3\. Filtering by category or property**

Find elements that match a particular category, parameter, type, or property value. This demonstrates selective querying rather than retrieving an entire collection.

Question: Find all Rooms whose Number parameter equals "8".

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "many",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Rooms"
          },
          {
            "field": "parameters.Number",
            "op": "eq",
            "value": "8"
          }
        ]
      }
    }
  ],
  "return": {
    "element": "$step1"
  }
}
```

Expected result: An array containing only Room records whose parameters.Number equals "8".

Actual results:

```json
{
  "element": [
    {
      "category": "Rooms",
      "hostId": null,
      "intId": 463787,
      "isEnclosed": true,
      "levelId": "26bea7fc-fb11-4ce2-99e8-8b3ac671ef08-0004d872",
      "parameters": {
        "Actual Lighting Load": "0 W",
        "Actual Lighting Load per area": "0.00 W/m²",
        "Actual Power Load": "0 W",
        "Actual Power Load per area": "0.00 W/m²",
        "Area": "878.8212 ft²",
        "Area per Person": "307.5403 ft²",
        "Base Lighting Load on": "By Space Type",
        "Base Offset": "0 ft",
        "Base Power Load on": "By Space Type",
        "Category": "Rooms",
        "Computation Height": "0 ft",
        "Design Option": "-1",
        "Export to IFC": "By Type",
        "Heat Load Values": "By Space Type",
        "IfcGUID": "3YCVvZ0XjCg9lSl179MzQE",
        "Latent Heat Gain per person": "59 W",
        "Level": "Level 2",
        "Lighting Load Units": "Power Density",
        "Limit Offset": "13.1234 ft",
        "Name": "Room",
        "Number": "8",
        "Number of People": "0",
        "Perimeter": "122.458 ft",
        "Phase": "New Construction",
        "Phase Id": "0",
        "Plenum Lighting Contribution": "20.00%",
        "Power Load Units": "Power Density",
        "Sensible Heat Gain per person": "73 W",
        "Specified Lighting Load": "0 W",
        "Specified Lighting Load per area": "10.76 W/m²",
        "Specified Power Load": "0 W",
        "Specified Power Load per area": "13.99 W/m²",
        "Total Heat Gain per person": "132 W",
        "Unbounded Height": "13.1234 ft",
        "Upper Limit": "Level 2"
      },
      "parentId": null,
      "roomAreas": {
        "activeMethod": "Finish",
        "unit": "Square meters",
        "values": {
          "Center": 86.39726331249952,
          "CoreBoundary": 81.64515849999954,
          "CoreCenter": 86.39726331249952,
          "Finish": 81.64515849999957
        }
      },
      "surroundingCeilings": null,
      "surroundingFloors": [
        {
          "facingArea": "82 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 81.64515849999955,
          "uniqueId": "1232ba24-79d0-4032-b8cb-e1d82c651922-0004e468"
        }
      ],
      "surroundingRoofs": null,
      "surroundingWalls": [
        {
          "facingArea": "40 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 39.593749999999716,
          "uniqueId": "a4e92ee7-0c45-4855-a5bd-a7ec508b10c4-0004fb52"
        },
        {
          "facingArea": "20 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 20.392049999999987,
          "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063c82"
        },
        {
          "facingArea": "3 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 3.4518749999997382,
          "uniqueId": "567befd4-f0d5-4aec-9716-c79bc8088971-00063dce"
        },
        {
          "facingArea": "5 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 4.89579999999999,
          "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063c44"
        },
        {
          "facingArea": "36 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 36.141875,
          "uniqueId": "567befd4-f0d5-4aec-9716-c79bc8088971-00063f7a"
        },
        {
          "facingArea": "26 m²",
          "facingAreaUnit": "Square meters",
          "facingAreaValue": 25.725349999999995,
          "uniqueId": "a4e92ee7-0c45-4855-a5bd-a7ec508b10c4-0004fbc0"
        }
      ],
      "typeId": null,
      "uniqueId": "e231fe63-021b-4ca8-9bdc-bc11c95cc525-000713ab"
    }
  ]
}
```

## **4\. Following element relationships**

Start from one element and follow references such as `typeId`, `hostId`, `parentId`, or `levelId` to retrieve related records. This demonstrates how BuildingDNA connects elements semantically.

Question: Which Wall hosts Door instance intId 406391?

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "one",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Doors"
          },
          {
            "field": "intId",
            "op": "eq",
            "value": 406391
          }
        ]
      }
    },
    {
      "id": "step2",
      "op": "select",
      "expect": "one",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Walls"
          },
          {
            "field": "uniqueId",
            "op": "eq",
            "value": "$step1.hostId"
          }
        ]
      }
    }
  ],
  "return": {
    "element": "$step2"
  }
}
```

Expected result: The hosting Wall record, whose uniqueId matches the Door’s hostId.

Actual results:

```json
{
  "element": {
    "category": "Walls",
    "hostId": null,
    "intId": 405383,
    "levelId": null,
    "parameters": {
      "Area": "442.8711 ft²",
      "Base Constraint": "Level 2",
      "Base Extension Distance": "0 ft",
      "Base Offset": "0 ft",
      "Base is Attached": "No",
      "Category": "Walls",
      "Cross-Section": "Vertical",
      "Design Option": "-1",
      "Export to IFC": "By Type",
      "Family": "Basic Wall",
      "Family and Type": "Basic Wall: Storaenso Wall - INTERNAL WALL 1,03 (1)",
      "Hosted": "No",
      "IfcGUID": "2DBsWF0qrAQ9TdkXPZRqYe",
      "Length": "43.9674 ft",
      "Location Line": "Wall Centerline",
      "Phase Created": "New Construction",
      "Phase Demolished": "None",
      "Related to Mass": "No",
      "Room Bounding": "Yes",
      "Structural": "No",
      "Structural Usage": "Non-bearing",
      "Top Constraint": "Up to level: Level 3",
      "Top Extension Distance": "0 ft",
      "Top Offset": "0 ft",
      "Top is Attached": "No",
      "Type": "Storaenso Wall - INTERNAL WALL 1,03 (1)",
      "Type Id": "404806",
      "Unconnected Height": "11.4829 ft",
      "Volume": "181.6237 ft³"
    },
    "parentId": null,
    "typeId": "8d2f680f-034d-4a68-9767-ba166369672f-00062d46",
    "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00062f87"
  }
}
```

## **5\. Room relationships**

Query spatial relationships such as which Room contains an element or which Rooms are connected through a Door. This demonstrates use of `roomId`, `fromRoomId`, and `toRoomId`.

Question: Which Rooms are connected through Door instance intId 406391?

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "one",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Doors"
          },
          {
            "field": "intId",
            "op": "eq",
            "value": 406391
          }
        ]
      }
    },
    {
      "id": "step2",
      "op": "select",
      "expect": "many",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Rooms"
          }
        ]
      }
    },
    {
      "id": "step3",
      "op": "calculate",
      "expression": {
        "operation": "cel",
        "source": "items.filter(room, (has(door.fromRoomId) && door.fromRoomId != null && room.uniqueId == door.fromRoomId) || (has(door.toRoomId) && door.toRoomId != null && room.uniqueId == door.toRoomId)).map(room, {'roomNumber': has(room.parameters.Number) ? room.parameters.Number : null, 'uniqueId': room.uniqueId})",
        "variables": {
          "items": {
            "from": "$step2"
          },
          "door": {
            "from": "$step1"
          }
        }
      }
    }
  ],
  "return": {
    "value": "$step3"
  }
}
```

Expected result: An array containing only roomNumber and uniqueId for each connected Room. The Room records are referenced by the Door’s fromRoomId or toRoomId. Missing or null references produce no match; each Room appears once.

Actual results:

```json
{
  "value": [
    {
      "roomNumber": "12",
      "uniqueId": "e231fe63-021b-4ca8-9bdc-bc11c95cc525-000713b7"
    },
    {
      "roomNumber": "54",
      "uniqueId": "e231fe63-021b-4ca8-9bdc-bc11c95cc525-00071454"
    }
  ]
}
```

## **6\. Room area and enclosure information**

Retrieve Room area values under different boundary methods, the active area method, or `isEnclosed`. This demonstrates BuildingDNA specific spatial information beyond ordinary parameters.

Question: For every Room, return its intId, roomAreas, and isEnclosed.

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "many",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Rooms"
          }
        ]
      }
    },
    {
      "id": "step2",
      "op": "calculate",
      "expression": {
        "operation": "cel",
        "source": "items.map(room, {'intId': room.intId, 'roomAreas': room.roomAreas, 'isEnclosed': room.isEnclosed})",
        "variables": {
          "items": {
            "from": "$step1"
          }
        }
      }
    }
  ],
  "return": {
    "value": "$step2"
  }
}
```

Expected result: An array containing each Room’s intId, enclosure boolean, and roomAreas object with activeMethod, unit, and values for Finish, Center, CoreBoundary, and CoreCenter.

Actual results (excerpt for the only first two records):

```json
{
  "value": [
    {
      "intId": 463762,
      "isEnclosed": true,
      "roomAreas": {
        "activeMethod": "Finish",
        "unit": "Square meters",
        "values": {
          "Center": 1154.5647749999832,
          "CoreBoundary": 1132.7667749999835,
          "CoreCenter": 1154.5647749999832,
          "Finish": 1132.7667749999835
        }
      }
    },
    {
      "intId": 463768,
      "isEnclosed": true,
      "roomAreas": {
        "activeMethod": "Finish",
        "unit": "Square meters",
        "values": {
          "Center": 86.39615624999962,
          "CoreBoundary": 81.64407656249965,
          "CoreCenter": 86.39615624999962,
          "Finish": 81.64407656249963
        }
      }
    },
```

## **7\. Surrounding elements**

Retrieve the Walls, Floors, Ceilings, or Roofs surrounding a Room, potentially including facing areas. This demonstrates spatial boundary relationships.

Question: For Room number 10, return its intId and surrounding Walls, Floors, Ceilings, and Roofs, including the exported facing areas.

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "one",
      "from": "elements",
      "where": {
        "match": "all",
        "filters": [
          {
            "field": "category",
            "op": "eq",
            "value": "Rooms"
          },
          {
            "field": "parameters.Number",
            "op": "eq",
            "value": "10"
          }
        ]
      }
    }
  ],
  "return": {
    "value": {
      "intId": "$step1.intId",
      "surroundingWalls": "$step1.surroundingWalls",
      "surroundingFloors": "$step1.surroundingFloors",
      "surroundingCeilings": "$step1.surroundingCeilings",
      "surroundingRoofs": "$step1.surroundingRoofs"
    }
  }
}
```

Expected result: One object for Room 10 containing its intId and four surrounding element lists, preserving the stored uniqueId references and facing area data.

Actual results:

```json
{
  "value": {
    "intId": 463793,
    "surroundingCeilings": null,
    "surroundingFloors": [
      {
        "facingArea": "86 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 86.05388924999959,
        "uniqueId": "1232ba24-79d0-4032-b8cb-e1d82c651922-0004e468"
      }
    ],
    "surroundingRoofs": null,
    "surroundingWalls": [
      {
        "facingArea": "33 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 33.20589999999984,
        "uniqueId": "a4e92ee7-0c45-4855-a5bd-a7ec508b10c4-0004fbc0"
      },
      {
        "facingArea": "36 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 36.141875,
        "uniqueId": "567befd4-f0d5-4aec-9716-c79bc8088971-00064185"
      },
      {
        "facingArea": "14 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 13.920899999999866,
        "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063c44"
      },
      {
        "facingArea": "7 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 7.1312499999999766,
        "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063ab1"
      },
      {
        "facingArea": "19 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 19.284999999999982,
        "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-0006352f"
      },
      {
        "facingArea": "29 m²",
        "facingAreaUnit": "Square meters",
        "facingAreaValue": 28.57312500000002,
        "uniqueId": "8d2f680f-034d-4a68-9767-ba166369672f-00063492"
      }
    ]
  }
}
```

## **8\. Building and site context**

Retrieve document level information such as building area, occupancy classifications, or the finished grade reference. This demonstrates information that is not attached to a single element record.

Question: Retrieve the model’s buildingContext and siteContext.

```json
{
  "steps": [
    {
      "id": "step1",
      "op": "select",
      "expect": "one",
      "from": "context"
    }
  ],
  "return": {
    "value": "$step1"
  }
}
```

Expected result: One object containing buildingContext and siteContext, including the exported building area, occupancy classifications, and site information.

Actual results:

```json
{
  "value": {
    "buildingContext": {
      "buildingArea": "2230 m²",
      "buildingAreaSource": "UserProvided",
      "buildingAreaUnit": "Square meters",
      "buildingAreaValue": 2230,
      "majorOccupancyClassifications": [
        "A1",
        "A2",
        "A3",
        "A4"
      ]
    },
    "siteContext": {
      "finishedGradeReferenceId": null,
      "finishedGradeReferencePhaseId": null,
      "finishedGradeReferenceType": null
    }
  }
}
```

