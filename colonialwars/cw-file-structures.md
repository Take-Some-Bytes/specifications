# Colonial Wars File Structures
This document defines JSON schemas for Colonial Wars Files, namely, the Map Save File,
the Unit Data File, the Building Data File, and the Graphics Data File. This obsoletes
the ``file-structures.md`` file, which only defined Map Save Files (referred to in the
document as a Save File).

Draft Revision 5.

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
    "tree1": {
      // Must match the corresponding key (look up).
      "id": "tree1",
      // A name for the current graphic.
      // May only have alphanumeric characters and spaces.
      "name": "Tree 1",
      // The path to the file to take this graphic's image and animations from.
      "file": "/imgs/game-images/trees-sheet.png",
      // How many angles the specified graphic has. May only be 1, 2, 4, or 8.
      "angles": 1,
      // Must be a boolean.
      "hasAnimations": false,
      // Data for the main image that will be show in-game, in information panels
      // and/or as the actual entity.
      "mainImg": {
        // The X and Y position of the image, relative to the file named above.
        "x": 0,
        "y": 0,
        // The width and height of the image.
        "w": 200,
        "h": 250
      },
      // The following are optional images that MAY be shown in-game. See more in
      // Section 6.1.2.
      "damaged1Img": null,
      "damaged2Img": null,
      "constructingImg": null,
      // Animations the graphic has. This MUST be null if "hasAnimations" is false.
      // Otherwise, it MUST be an object.
      "animations": null
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
- ``"file"``: This MUST be a string, AND a valid path to an image file.
- ``"angles"``: This specifies how many angles the specified graphic has. It may only
be 1, 2, 4, or 8. See [Section 6.1.1](#6.1.1.-graphic-angles) for how this affects a
graphic's animations and main image.
- ``"hasAnimations"``: This specifies whether the current graphic has animations
associated with it. It MUST be a boolean.
- ``"mainImg"``: See [Section 6.1.2](#6.1.2.-graphic-images).
- ``"animations"``: See [Section 6.1.3](#6.1.3.-graphic-animations).

If a graphics data parser encounters animation-related fields (in the ``"animations"`` object)
and the ``"hasAnimations"`` field is false, the animations MUST be ignored. If the
``"hasAnimations"`` field is true, but there are zero animation fields, an error
MUST be thrown.

#### 6.1.1. Graphic Angles
Since units would likely have to be able to face left, right, forward, and backward, a graphic
could have angles. A graphic could have either 1, 2, 4, or 8 angles. The number of angles a
graphic has defines how the renderer draws the graphic, and how the image needs to be layed out.

For example, if a graphic has 4 angles, the underlying file for the graphic must also have 4
angles for each [image](#6.1.2.-graphic-images) and [animation](#6.1.3.-graphic-animations) of
the graphic. This means that, for example, the part of the underlying file that houses the
Main image of the graphic must contain 4 angles of the same image, stacked on top of each
other, starting with the image that faces forward on the top, and moving down clockwise.
In ASCII art, it MAY look like this:
```none
               __________________
Main Image -[ |                  |
            [ |  Forward-facing  |
            [ |       Image      |
            [ |                  |
            [ |                  |
            [ +------------------+
            [ |                  |
            [ |    Left-facing   |
            [ |       Image      |
            [ |                  |
            [ |                  |
            [ +------------------+
            [ |                  |
            [ |    Back-facing   |
            [ |       Image      |
            [ |                  |
            [ |                  |
            [ +------------------+
            [ |                  |
            [ |   Right-facing   |
            [ |       Image      |
            [ |                  |
            [ |                  |
            [ +------------------+
```

(Of course, you don't have to arrange your sub-images like this, but if you don't, the renderer
would draw your entities as if they were walking backward all the time.)

The same rules apply for graphic animations.

#### 6.1.2. Graphic Images
A graphic could have multiple *static* images associated with it. The allowed static images
are: Main (``"mainImg"``), Damaged (``"damaged1Img"``), Heavily Damaged (``"damaged2Img"``),
and Constructing (``"constructing1Img"``). Damaged, Heavily Damaged, and Constructing are only
used for buildings.

A graphics data parser SHOULD ignore unrecognized image fields.

The Main image (``"mainImg"``) specifies the main image the graphic will be represented
with in-game. The ``"mainImg"`` will be used for purposes such as display in buttons which
wish to use the graphic's image, and may directly represent an entity if it has no
animations.

The Damaged image (``"damaged1Img"``) specifies the image that will be shown when the
entity using this graphic is moderately damaged. This image only applies to buildings.

The Heavily Damaged image (``"damaged2Img"``) specifies the image that will be shown
when the entity using this graphic is heavily damaged. This image only applies to buildings.

The Constructing image (``"constructingImg"``) specifies the image that will be shown
when the entity using this graphic is currently under construction. This image only applies
to buildings.

#### 6.1.3. Graphic Animations
A graphic could also have multiple *dynamic* animations associated with it.

If ``"hasAnimations"`` is true, then animations MUST be specified for the current
graphic. An example graphic with animations MAY look like this:
```jsonc
{
  // Everything else is the same, *except* we now have animations.
  "cannon1": {
    "id": "cannon1",
    "name": "Siege Cannon",
    "file": "/imgs/game-images/units/siege-cannon.png",
    "angles": 4,
    "hasAnimations": true,
    "mainImg": { "x": 0, "y": 0, "w": 40, "h": 40 },
    // Now, for actual animations.
    "animations": {
      // Death Animation: when the unit dies.
      "die": { "x": 160, "y": 160, "w": 320, "h": 160, "frameSize": 40 },
      // Idle Animation: when the unit is idle.
      "idle": { "x": 320, "y": 320, "w": 320, "h": 160, "frameSize": 40 },
      // Walk Animation: when the unit is walking.
      "walk": { "x": 480, "y": 480, "w": 320, "h": 160, "frameSize": 40 },
      // Attack Animation: when the unit attacks.
      "attack": { "x": 640, "y": 640, "w": 320, "h": 160, "frameSize": 40 },
      // Reload Animation: when the unit reloads.
      "reload": { "x": 800, "y": 800, "w": 320, "h": 160, "frameSize": 40 },
    }
  }
}
```

The full list of acceptable animations are:
- Death Animation (``"die"``): Played when the entity using this graphic dies.
- Idle Animation (``"idle"``): Played when the entity using this graphic is just
standing around and not doing anything. This will be played a max of once per 6 seconds.
- Walk Animation (``"walk"``): Played when the entity using this graphic is walking.
- Attack Animation (``"attack"``): Played when the entity using this graphic attacks.
- Reload Animation (``"reload"``): Played when the entity using this graphic is reloading
its weapon. Only applies to ranged entities.
- Busy Animation (``"busy"``): Played when the entity using this graphic is busy doing
something (e.g. training a unit, building a mechanical unit, etc). Only applies to buildings
- Cast Animation (``"cast"``): Played when the entity using this graphic "casts" (or
throws) something (e.g. a spell, or a grenade).
- Damaged, Busy Animation (``"busyDamaged1"``): Played when the entity using this graphic
is busy doing something, and is moderately damaged. Only applies to buildings
- Heavily Damaged, Busy Animation (``"busyDamaged2"``): Played when the entity using this
graphic is busy doing something, and is heavily damaged. Only applies to buildings.
