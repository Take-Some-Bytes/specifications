# Colonial Wars File Structures
This document defines JSON schemas for Colonial Wars Files, namely, the Map Save File,
the Unit Data File, the Building Data File, and the Graphics Data File. This obsoletes
the ``file-structures.md`` file, which only defined Map Save Files (referred to in the
document as a Save File).

Draft Revision 4.

## 1. Conformance Requirements
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT",
"RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in
[RFC2119](https://tools.ietf.org/html/rfc2119).

Requirements phrased in the imperative as part of algorithms (such as "strip any leading space
characters" or "return false and abort these steps") are to be interpreted with the meaning
of the key word ("MUST", "SHOULD", "MAY", etc.) used in introducing the algorithm.

This document's requirements are _CASE SENSITIVE_, meaning that 'config-type' is not the same
as 'CONFIG-TYPE' or 'cONfIg-TYpe'.

## 1.1. Terminology and Other Conventions
_Map_ shall refer to an environment in which a player of Colonial Wars will play
in; all aspects of gameplay MUST occur on the _Map_.

Any text enclosed in angled brackets (&lt;like this&gt;) means "replace this text with an
appropriate value".

## 2. General File Format
The general file format for Colonial Wars Files is as follows:
```json
{
  "configType": "<string>",
  "meta": {},
  "data": {}
}
```
The ``"configType"`` field MUST have one of these string values:
- ``map-config``: This denotes a config file for a Map Save File. See [Section 3](#3.-map-save-file).
- ``unit-data``: This denotes a config file for a Unit Data File. See [Section 4](#4.-unit-data-file).
- ``building-data``: This denotes a config file for a Building Data File. See [Section 5](#5.-building-data-file).
- ``graphics-data``: This denotes a config file for a Graphics Data File. See [Section 6](#6.-graphics-data-file).

The contents of the ``meta`` and ``data`` fields depend on the configuration type.

## 3. Map Save File
For a JSON file to be treated as a Map Save File, its ``"configType"`` field MUST have the value
``map-config``.

A Map Save File's structure is as follows:
```jsonc
{
  "configType": "map-config",
  "meta": {
    // Must only have alphanumerical characters and spaces
    "name": "Valley",
    // Either teams, koth, or siege. Case-insensitive.
    "mode": "teams",
    // Must be a positive integer
    "maxPlayers": 40,
    "worldLimits": {
      // Must have x and y, and those properties must have a number describing
      // the maximums for each axis.
      "x": 6000,
      "y": 6000
    },
    "teams": [
      // Must be an array of objects with the following structure.
      // The array must have more than one element.
      {
        // Must be a string with alphanumeric characters.
        "name": "British",
        // Must have an x and y axis.
        "spawnPosition": { "x": 100, "y": 100 },
        // A small description of the team. Must be less than 150 characters.
        "description": "The British.",
        // Must be a positive integer. This describes the maximum amount
        // of players on this team.
        "maxPlayers": 10
      },
      {
        "name": "French",
        "spawnPosition": { "x": 5900, "y": 5900 },
        "description": "The French.",
        "maxPlayers": 10
      }
    ],
    // Must be rock, grass, or sand.
    "tileType": "grass",
    // Must be 0, 1, or 2
    "defaultHeight": 0,
    // Must be a string containing a description of the map.
    "description": "A fight in the lush valley.",
    // This specifies a Unit Data File which this Map Save File's unit data
    // extends from. Must be "none" if this Map Save File does not extend unit data
    "unitDataExtends": "default",
    // This specifies a Building Data File which this Map Save File's building data
    // extends from. Must be "none" if this Map Save File does not extend unit data
    "buildingDataExtends": "default",
    // This specifies a Graphics Data File which this Map Save File's graphics data
    // extends from. Must be "none" if this Map Save File does not extend unit data
    "graphicsDataExtends": "default"
  },
  "data": {
    // Must be an object, with only lowercase letters and underscores as its keys.
    // See Section 6.1 for details.
    "graphicsData": {}
  }
}
```

### 3.1. Game Modes
A Map Save File MUST specify a game mode in which the game is to be played in. The ``"mode"`` property
specifies the game mode. It is case-insensitive.

#### 3.1.1. Teams Mode
The ``"teams"`` game mode denotes a game in which the specified teams are supposed to fight
until only one team is left standing. How teams eliminate each other is up to the implementer
to decide.

#### 3.1.2. KOTH (King of the Hill) Mode
The ``"koth"`` game mode denotes a game in which a team has to capture a given point and
hold that point for a specific amount of time. Contrary to the name of this mode, the
point-to-capture does not have to be a hill.

#### 3.1.3. Siege Mode
The ``"siege"`` game mode denotes a game in which one team defends a given position
for a specific amount of time, while the other team(s) try to capture that point.

### 3.2. File Dependencies
A Map Save File could have dependencies; namely, it could request to use an existing
Unit, Building, or Graphics Data File in the game, and possibly extend them if it wants.

A Map Save File could define dependencies using the ``"unitDataExtends"``, ``"buildingDataExtends"``,
and ``"graphicsDataExtends"`` fields.

Those fields could either specify a file path, OR they could use the keyword ``"default"``.
The meaning of ``"default"`` is left up to the implementer. It MAY map to a file path.

If a Map Save File has no dependencies, its ``"unitDataExtends"``, ``"buildingDataExtends"``,
and ``"graphicsDataExtends"`` fields MUST all have the absolute value ``"none"``.

## 4. Unit Data File
TODO
## 5. Building Data File
TODO
## 6. Graphics Data File
For a JSON file to be treated as a Graphics Data File, its ``"configType"`` field MUST have the
absolute value ``graphics-data``.

A Graphics Data File's structure is as follows:
```jsonc
{
  "configType": "graphics-data",
  "meta": {
    // Must only have alphanumeric characters and spaces.
    "name": "Default Graphics"
  },
  // The "data" object's keys may only contain lowercase letters, numbers,
  // and underscores. Every value MUST be an object.
  "data": {
    "flag": {
      // Must match the corresponding key (look up).
      "id": "flag",
      // A name for the current graphic.
      // May only have alphanumeric characters and spaces.
      "name": "Flag",
      // The path to the image to take this graphic from.
      "imgPath": "/imgs/game-images/buildings-sheet.png",
      // The start position of the sub-image to take from the file at
      // "imgPath". This is useful for sprite sheets.
      // Both x and y must be integers less than or equal to the
      // width and height of the image, and greater than 0.
      "position": { "x": 0, "y": 0 },
      // The dimensions of the sub-image to take from the file at
      // "imgPath". This is useful for sprite sheets.
      "dimensions": { "width": 50, "height": 300 }
    }
  }
}
```

### 6.1. Specifying a Graphic
To specify a graphic, a new value must be entered into the "data" object of the
Graphics Data File. Each value's key must be a string that only contains lowercase
letters, numbers, and underscores.

The corresponding value MUST be an object. Each graphics object must have the
following fields:
- ``"id"``: This MUST match the graphics object's key.
- ``"name"``: This MUST be a string, with the above limitations.
- ``"imgPath"``: This MUST be a string, AND a valid path to an image file.
- ``"position"``: This MUST be an object with "x" and "y" as its properties. This
specifies the position of the top-left corner of the graphic, relative to the
size of the image at ``"imgPath"``.
- ``"dimensions"``: This MUST be an object with "width" and "height" as its properties.
This specifies the dimensions of the graphic.
