# Colonial Wars File Structures
This document defines JSON schemas for Colonial Wars Files, namely, the Map Save File,
the Unit Data File, the Building Data File, and the Graphics Data File. This obsoletes
the ``file-structures.md`` file, which only defined Map Save Files (referred to in the
document as a Save File).

Draft Revision 9.

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

Any and all comments contained in JSONC (JSON with comments) code blocks MUST be interpreted as imperative commands.

The phrase "MAY ONLY" (case-insensitive) is to be interpreted as "MUST".

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
- ``obstacles-data``: This denotes a config file for a Obstacles Data File. See [Section 7](#7.-obstacles-data-file).
- ``abilities-data``: This denotes a config file for an Abilities Data File. See [Section 8](#8.-abilities-data-file).
- ``modifiers-data``: This denotes a config file for a Modifiers Data File. See [Section 9](#9.-modifiers-data-file).

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
    // Must be either teams, koth, or siege. Case-insensitive.
    "mode": "teams",
    // Must be a positive number equal to the maximum players of each team
    // combined.
    "maxPlayers": 20,
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
        // Must be a positive integer greater than zero. This describes the
        // maximum amount of players on this team.
        "maxPlayers": 10
      },
      // There MUST be at least two teams on a map.
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

### 3.2. Map Teams
A map may have multiple teams, which players will fight for. Multiple players may be on a team.
Teams with duplicate names are not allowed.

A map MUST have at least 2 (two) teams, and at most 8 (eight). The spawn position of each team
SHOULD NOT overlap, unless you want carnage at the very start of each game.

### 3.3. File Dependencies
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
    "tree_img_1": {
      // Must match the corresponding key (look up).
      "id": "tree_img_1",
      // A name for the current graphic.
      // MAY ONLY have alphanumeric characters and spaces.
      "name": "Tree Graphic 1",
      // The path to the file to take this graphic's image and animations from.
      // MUST be a valid file path.
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
- ``"name"``: This MUST be a string, which may only have alphanumeric characters and spaces.
- ``"file"``: This MUST be a string, AND a valid path to an image file.
- ``"angles"``: This specifies how many angles the specified graphic has. It may only be 1,
2, 4, or 8. See [Section 6.1.1](#6.1.1.-graphic-angles) for how this affects a graphic's
animations and main image.
- ``"hasAnimations"``: This specifies whether the current graphic has animations associated
with it. It MUST be a boolean.
- ``"mainImg"``, ``"damaged1Img"``, ``"damaged2Img"``, ``"constructing1Img"``: See
[Section 6.1.2](#6.1.2.-graphic-images).
- ``"animations"``: See [Section 6.1.3](#6.1.3.-graphic-animations).

If a graphics data parser encounters animation-related fields (in the ``"animations"`` object)
and the ``"hasAnimations"`` field is false, the animations MUST be ignored. If the
``"hasAnimations"`` field is true, but there are zero animation fields, an error MUST be thrown.

#### 6.1.1. Graphic Angles
Since units would likely have to be able to face left, right, forwards, and backwards, a graphic
could have angles. A graphic could have either 1, 2, 4, or 8 angles. The number of angles a
graphic has defines how the renderer draws the graphic, and how the image needs to be layed out.

For example, if a graphic has 4 angles, the underlying file for the graphic must also have 4
angles for each [image](#6.1.2.-graphic-images) and [animation](#6.1.3.-graphic-animations) of
the graphic. This means that, for example, the part of the underlying file that houses the
Main image of the graphic must contain 4 angles of the same image, stacked on top of each
other, starting with the image that faces forward on the top, and moving down clockwise.
In ASCII art, it may look like this:
```none
       [ +------------------+
Image -[ |                  |
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

(Of course, you don't have to arrange your sub-images like this, but it is highly RECOMMENDED
to do so. If you don't, the renderer
wouldn't render your graphics properly.)

The same rules apply for graphic animations.

**NOTE**: for graphic animations, all the animations for a single angle *must* be in a single
line, like this:
```none
             [ +------------------+------------------+
(Animation) -[ |                  |                  |
             [ |  Forward-facing  |  Forward-facing  |
             [ |    animation     |    animation     | ...Etc.
             [ |     frame 1      |     frame 2      |
             [ |                  |                  |
             [ +------------------+------------------+
```

#### 6.1.2. Graphic Images
A graphic could have multiple *static* images associated with it.

Example:
```jsonc
{
  "house": {
    // Other fields omitted.
    "mainImg": {
      // X position of the top-left corner of the image.
      "x": 0,
      // Y position of the top-left corner of the image.
      "y": 0,
      // Width and height of the image.
      "w": 100,
      "h": 100
    }
  }
}
```
The ``"x"`` and ``"y"`` properties specify the relative coordinates of the top left corner of
the static image. The ``"w"`` and ``"h"`` properties specify the total width and height of the
image.

If all fields of a static image are [falsy](
  https://developer.mozilla.org/en-US/docs/Glossary/Falsy
), the parser MUST assume that the static image is equivalent to the JSON value ``null``.

Field values may not be negative or floating point. An error MUST
be thrown if a negative number or a floating point number is encountered.

The full list of acceptable static images are:
- Main Image (``"mainImg"``): specify the main image the graphic will be represented
with in-game. The ``"mainImg"`` will be used for purposes such as display in buttons which
wish to use the graphic's image, and may directly represent an entity if it has no
animations. This image is REQUIRED.
- Damaged Image (``"damaged1Img"``): specify the image that will be shown when the
entity using this graphic is moderately damaged. This image only applies to buildings.
- Heavily Damaged Image (``"damaged2Img"``) specifies the image that will be shown
when the entity using this graphic is heavily damaged. This image only applies to buildings.
- Constructing Image (``"constructingImg"``) specifies the image that will be shown
when the entity using this graphic is currently under construction. This image only applies
to buildings.

A graphics data parser SHOULD ignore unrecognized static image fields.

#### 6.1.3. Graphic Animations
A graphic could also have multiple *dynamic* animations associated with it.

If ``"hasAnimations"`` is true, then animations MUST be specified for the current
graphic. An example graphic with animations MAY look like this:
```jsonc
{
  // Everything else is the same, *except* we now have animations.
  "cannon_img_1": {
    "id": "cannon_img_1",
    "name": "Cannon Graphic 1",
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

The ``"x"`` and ``"y"`` properties of an animation specifies the relative coordinates where
the frames for the animation begins. The ``"width"`` and ``"height"`` properties specify the
total width and height of all the frames combined. The ``"frameSize"`` property specifies
the size of each frame.

If all fields of a dynamic animation are [falsy](
  https://developer.mozilla.org/en-US/docs/Glossary/Falsy
), the parser MUST assume that the animation is equivalent to the JSON value ``null``.

Field values may not be a negative number or a floating point number. An error MUST
be thrown if a negative number or a floating point number is encountered.

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

A graphics data parser SHOULD ignore unrecognized animation fields.

## 7. Obstacles Data File
An Obstacles Data File defines a set of *physical obstacles* that could be present on a Colonial
Wars map. Examples of entities suitable to treat as an obstacle are: trees, water, stones, or
flowers. Obstacles cannot be destroyed, but other entities may be able to walk or be built on it.

For a JSON file to be treated as a Obstacles Data File, its ``"configType"`` field MUST have the
absolute value ``obstacles-data``.

An Obstacles Data File's structure is as follows:
```jsonc
{
  "configType": "obstacles-data",
  "meta": {
    "name": "Example Obstacles File"
  },
  "data": {
    "tree1": {
      // Must match the key of this value.
      "id": "tree",
      // May only have alphanumeric characters and spaces.
      // The name of the obstacle.
      "name": "Tree 1",
      // A description of the obstacle. Not required in-game, only for
      // when editing maps.
      "description": "A tree.",
      // Must contain the ID of a valid graphic.
      "img": "tree_img_1",
      // Set the scale of the image.
      "imgScale": 1,
      // Specify the obstacle type.
      "isSolid": true,
      "isLiquid": false,
      "isDecoration": false,
      // The depth of the current obstacle. Ignored if isLiquid is false.
      "depth": 0,
      // Specific entities to allow if isLiquid is true.
      "allowDocks": false,
      "allowBoats": false,
      // Configure entity blocking. Ignored if isLiquid is true.
      "blocksEntities": true,
      "blocksEntitiesFilter": [],
      "blocksEntitiesFilterExclude": [],
      // Set the dimensions of this obstacle. Ignored if isLiquid or
      // isDecoration is true.
      "dimensions": {
        "w": 100,
        "l": 100,
        "h": 500
      },
      // A list of modifiers that get applied when an entity walks over
      // this obstacle. Ignored if isDecoration is true.
      "modifiers": [],
      // A list of modifiers this obstacle spawns with. Ignored if isDecoration is true.
      "spawnModifiers": [],
      // A sound to play while an entity is MOVING through this obstacle.
      // Ignored if isDecoration is true.
      "sound": "",
      // How loud to play the sound. Ignored if isDecoration is true.
      "soundVolume": 1,
    }
  }
}
```

### 7.1. Specifying an Obstacle
To specify an obstacle, a new value must be entered into the ``"data"`` object of the Obstacles
Data File. Each new value's key MUST be a string that only contains lowercase letters, numbers,
and underscores.

The new value MUST be an object, with the following fields:
- ``"id"``: This MUST match the current obstacle's key. An obstacle's ID may only have lowercase
letters, underscores and numbers.
- ``"name"``: This MUST be a string, which may only have alphanumeric characters and spaces.
- ``"img"``: This MUST be a string, and it MUST be a valid graphic's ID. This specifies the
graphic this obstacle will use.
- ``"imgScale"``: This specifies the amount to scale the selected graphic for this entity by.
This field MUST be an integer, and MUST be interpreted as a percentage in decimal format
(e.g. 1 == 100%).
- ``"isLiquid"``, ``"isSolid"``, ``"isDecoration"``: See [Section 7.2](#7.2.-obstacle-type). At
least *one* of these properties MUST be ``true``.
- ``"isLiquid"``, ``"depth"``, ``"allowDocks"``, ``"allowBoats"``: See [Section 7.2.1](#7.2.1.-liquid-obstacles). These options are used to configure liquid obstacles.
- ``"blocksEntities"``, ``"blocksEntitiesFilter"``, ``"blocksEntitiesFilterExclude"``: Configure
if the current obstacle blocks entities from walking over it, and if so, which entities it blocks.
See [Section 7.3](#7.3-blocking-entities-from-passing-through-obstacles) for details.
- ``"dimensions"``: Specify the dimensions of the obstacle. Only applies to
[solid obstacles](#7.2.2.-solid-obstacles).
- ``"modifiers"``: This MUST be an array of strings. This specifies modifiers that get applied
to entities that walk over this obstacle.
- ``"spawnModifiers"``: This MUST be an array of strings. This specifies modifiers that the
current obstacle spawns with.
- ``"sound"``, ``"soundVolume"``: Options for playing a sound when an entity walks *on* the
current obstacle. See [Section 7.4](#7.4.-obstacles-and-sounds) for details.

### 7.2. Obstacle Type
An obstacle could either be a *liquid*, *solid*, or *decoration*. Liquid obstacles are free-form,
and could be placed anywhere. Solid obstacles have a specific size and height, and may not overlap.
Decorations are for decorative purposes only, may overlap, and have no effect on actual gameplay.

A good example of a liquid obstacle is water. See [Section 7.2.1](#7.2.1.-liquid-obstacles) about 
liquid obstacles. A good example of a solid obstacle is a tree. See
[Section 7.2.2](#7.2.2.-solid-obstacles) about solid obstacles. A good example of a decorative
obstacle is a flower. See [Section 7.2.3](#7.2.3.-decorative-obstacles) about decorative obstacles.

Both liquid and solid obstacles could be configured to *allow* entities to walk over (in the case
of liquids) or pass through (in the case of solids), as opposed to only allowing projectiles to
fly over them (in the case of liquids) or not allowing any entities through at all (in the case
of solids). To configure liquids to allow entities to walk over them, or to configure solids to
allow entities to pass through them, refer to
[Section 7.3](#7.3-blocking-entities-from-passing-through-obstacles).

As decorations do not have any effect on the actual gameplay, entities will be able to walk over
(and be built on) any decoration.

#### 7.2.1. Liquid Obstacles
A *liquid* obstacle (e.g. water) is a free-form obstacle that has no defined shape or size. To
specify a liquid, the ``"isLiquid"`` property of the current obstacle MUST be set to ``true``.

Liquid obstacles could be configured to allow boats and docks to be built on it, thus creating a
sea battle of sorts. The ``"allowDocks"`` and ``"allowBoats"`` MUST be set to ``true`` in order
to allow docks to be built on it and boats to sail on it, respectively.

The ``"depth"`` property of liquid obstacles specify how deep the liquid is in pixels. This MUST
be an unsigned integer greater than zero, and is REQUIRED. When walking in a liquid, entities will
sink ``"depth"`` pixels under the liquid. This is only graphical, and has not effect on gameplay.

The depth of a liquid could also determine if it blocks entities from walking on it. See
[Section 7.3.1](#7.3.1.-liquid-obstacles) for details.

#### 7.2.2. Solid Obstacles
A *solid* obstacle (e.g. a tree) is an obstacle which has a defined size and. Solid obstacles may
*not* overlap. To specify a solid, the ``"isSolid"`` property of the current obstacle MUST be set
to ``true``.

Solid obstacles MUST have a defined size. The size of a solid obstacle is specified with the
``"dimensions"`` object. The ``"w"`` and ``"l"`` properties set how wide and long an obstacle is,
respective, and the ``"h"`` property determines the height of the obstacle.

For example, the following ``"dimensions"`` object will specify an obstacle that covers 100x100
pixels of ground and is 280 pixels high.
```jsonc
{
  "dimensions": {
    "w": 100,
    "l": 100,
    "h": 280
  }
}
```

Entities could be blocked from passing through solid obstacles. See
[Section 7.3.2](#7.3.2.-solid-obstacles) for details.

#### 7.2.3 Decorative Obstacles
A *decorative* obstacle (e.g. a flower) is an "obstacle" that serves a purely decorative purpose.
Decorative obstacles will *not* have any effect on resulting gameplay. It will not block entities
from walking on it, and it may be placed anywhere on the ground or on top of a liquid obstacle.

The visual size of the obstacle will be determined by the size of the graphic the obstacle is
assigned.

### 7.3. Blocking Entities From Passing Through Obstacles
It is possible to block entities from passing through obstacles. Moreover, it is possible to
control *what* entities are blocked from passing through obstacles, and which are not. This gives
map editors a great deal of flexibility when dealing with obstacles.

There are two ways of specifying which entities an obstacle blocks: for liquid obstacles, the
value of the ``"depth"`` property determines if entities are blocked from walking over it. For
solid obstacles, the ``blocksEntities`` properties can be used to configure if entities are blocked,
which entities are blocked, and which entities are not blocked.

Decorative obstacles cannot block entities.

#### 7.3.1. Liquid obstacles
If the depth (specified by the ``"depth"`` property) of a liquid is equal to or greater than the
number 100, it will block entities from walking built over it. Boats are exempt from this.

Buildings will always be blocked from being built on liquids, regardless of liquid depth. Docks
are exempt from this.

#### 7.3.2. Solid obstacles
To block entities from passing through solid obstacles, the ``"blocksEntities"`` property MUST be
``true``. Entities are only blocked if they touch the solid obstacle's hitbox, which is calculated
using the values of the ``"dimensions"`` object.

It is also possible to control *which* entities are blocked, and which aren't. The 
``"blocksEntitiesFilter"`` array specifies a list of requirements an entity has to meet in order
to be blocked. The ``"blocksEntitiesFilterExclude"`` array, the inverse of ``"blocksEntitiesFilter'``,
specifies a list of requirements an entity MUST NOT meet in order to be blocked. The values of
both the above properties MUST be an array of strings.

### 7.4. Obstacles and Sounds
Sounds could be played whenever an entity *moves* over an obstacle. The sound will only be played
when an entity *moves*, and is inside the hitbox (in the case of solid obstacles) or on top
(in the case of liquid obstacles) of the current obstacle.

Use the ``"sound"`` field to specify the name of the sound to play. This field MUST have a string
value. Use the ``"soundVolume"`` field to set the sound's volume. This field MUST be an integer,
and MUST be interpreted as a percentage in decimal format (e.g. 1 == 100%).

Decorative obstacles will not be able to play sounds.

## 8. Abilities Data File
TODO

## 9. Modifiers Data File
Modifiers are effects that modify an entity's behaviour in-game. It may either be permanent,
or it could be temporary. Modifiers could only be applied to units and buildings.

For a JSON file to be treated as a Modifiers Data File, its ``"configType"`` field MUST have
the absolute value ``modifiers-data``.

A Modifiers Data File's structure is as follows:
```jsonc
{
  "configType": "modifiers-data",
  "meta": {
    // May only have alphanumeric characters and spaces.
    // This is just for identification purposes.
    "name": "Example Modifiers"
  },
  "data": {
    // Each key must only contain lowercase letters, numbers and underscores.
    "water_slowed": {
      // Must match the key of this value.
      "id": "water_slowed",
      // May only have alphanumeric characters and spaces.
      // The name of the modifier.
      "name": "Water Slowed",
      // A description of the modifier. May only have alphanumeric characters,
      // parentheses, commas, periods, ampersands, exclamation and question marks,
      // semicolons, colons, and spaces, and may only be 200 characters long.
      "description": "This unit is being slowed by water.",
      // How long the modifier lasts.
      "duration": -1,
      // How many times this modifier could stack.
      "maxStack": 1,
      // List of modifications this modifier will apply.
      "modifications": [
        {
          // The field to modify.
          "field": "movementSpeed",
          // How much to add to the field's value.
          "add": -1,
          // How much to multiply the resulting field value after adding the
          // value in "add".
          "multiply": 1
        }
      ],
      // A list of auras.
      "auras": [],
      // Aura hit filters.
      "auraHitsSelf": false,
      "auraHitsFriendly": false,
      "auraHitsAllied": false,
      "auraHitsEnemy": false,
      // Colour of the aura, in RGBA format.
      "auraColour": {
        "r": 255, "g": 255, "b": 255, "a": 1
      },
      // A list of requirements an entity has to meet in order for this effect
      // to be applied.
      "auraTargetFilters": [],
      // A list of requirements that, if any are met, will exclude the entity
      // from this aura.
      "auraTargetFiltersExclude": [],
      // A list of commands to disable while this modifier is being applied.
      "disableCommands": [],
      // Whether this modifier changes an entity's image.
      "changeEntityImg": false,
      // If changeEntityImg is true, the following graphic will be show in place
      // of the entity's original image.
      "entityImg": "none",
      // Whether this modifier changes an entity's attack effect.
      "changeAtkEffect": false,
      // If changeAtkEffect is true, the following effect will be show in place
      // of the entity's original attack effect.
      "atkEffect": "none",
      // A list of graphic effects to display on entities that have this modifier
      // applied.
      "effects": [],
      // The name of a sound to play while this modifier is being applied.
      "sound": "water_walk",
      // How loud to play the sound.
      "soundVolume": 1,
      // A list of other modifiers to remove when this modifier is applied.
      "killModifiers": []
    }
  }
}
```

### 9.1. Specifying a Modifier
To specify an modifier, a new value must be entered into the ``"data"`` object of the Modifiers
Data File. Each new value's key MUST be a string that only contains lowercase letters, numbers,
and underscores.

The new value MUST be an object, with the following fields:
- ``"id"``: MUST match the value's key.
- ``"name"``: A name for the modifier. MAY ONLY have alphanumeric characters and spaces.
- ``"description"``: A short description for the modifier. Must only have alphanumeric
characters, spaces, and the following punctuation (not including quotes): "(),.&!?;:".
A description MUST NOT be longer than 200 characters.
- ``"duration"``: How long the current modifier lasts once applied. 0 or a negative number
MUST be interpreted as Infinity, meaning the modifier lasts forever.
- ``"maxStack"``: How many times the current modifier could stack. 0 or a negative number
MUST be interpreted as Infinity, meaning the modifier could stack infinitely.
- ``"modifications"``: See [Section 9.2](#9.2.-applying-modifications).
- ``"auras"``, ``"auraHitsSelf"``, ``"auraHitsFriendly"``, ``"auraHitsAllied"``,
``"auraHitsEnemy"``,``"auraColour"``, ``"auraTargetFilters"``, ``"auraTargetFiltersExclude"``:
See [Section 9.3](#9.3.-specifying-auras).
- ``"disableCommands"``: A list of commands to disable while this modifier is being applied.
This MUST be an array of strings. If an unrecognized command is entered, it SHOULD be ignored.
- ``"changeEntityImg"``, ``"entityImg"``, ``"changeAtkEffect"`` ``"atkEffect"``: See
[Section 9.4](#9.4.-changing-images-when-a-modifier-is-applied).
- ``"effects"``: A list of graphic effects to display on entities who have this modifier applied.
- ``"sound"``, ``"soundVolume"``: See [Section 9.5](#9.5.-playing-sounds-when-a-modifier-is-applied).
- ``"killModifiers"``: A list of modifiers to remove from entities when they have this modifier
applied to them. This MUST be an array of strings. If an unrecognized modifier is listed here, it 
SHOULD be ignored.

### 9.2. Applying Modifications
The core of modifiers include applying *modifications* to properties of entities. Each modification
is structured like so:
```jsonc
{
  "field": "<valid entity field>",
  "add": 1,
  "multiply": 1
}
```

The ``"field"`` field specifies a field to modify. The specified field SHOULD be a valid entity
field, available to both [buildings](#5.-building-data-file) and [units](#4.-unit-data-file).
The ``"add"`` field specifies a numeric value to add to the specified field in ``"field"``.
It MAY be a negative number. If the field being modified is not numeric:
- If the field is a boolean field, add the value in the ``"add"`` field to the number 0 (zero).
If the resulting sum is equal to or smaller than 0 (zero), set the field being modified to
``false``. Otherwise, set the field to ``true``.
- Else, do nothing.

The ``"multiply"`` field specifies how much to multiply the value of the modified field after the
value in ``"add"`` has been added. This MUST be ignored for non-numeric fields.

### 9.3. Specifying Auras
In addition to directly modifying entity properties, a modifier could also specify other modifiers
to apply to entities in a certain range. This "modifier-applying-another-modifier" behaviour is
called *auras*.

A new aura could be specified by entering a new object into the ``"auras"`` field of a modifier.
Each item in the ``"auras"`` array MUST have the following structure:
```jsonc
{
  "modifier": "<valid modifier ID>",
  "range": 9 // Any positive integer.
}
```
The ``"modifier"`` field specifies the modifier to apply to entities within the range of the aura,
specified in the ``"range"`` field. The aura range MUST be a positive number. Parsers SHOULD ignore
an aura if its ``"range"`` field is not a positive integer greater than 0.

Multiple auras could be specified.

#### 9.3.1. Aura Filtering
The auras specified could be set to only apply to certain units. For that, the following properties
exist:
- The ``"auraHits"`` properties specify whether the aura hits enemy entities, allied entities, etc.
Here is a table of all ``"auraHits"`` properties:
  |          Field         |                                                        Purpose                                                       |
  |:----------------------:|:--------------------------------------------------------------------------------------------------------------------:|
  |   ``"auraHitsSelf"``   |                             Specify whether the aura(s) hit the entity emitting the aura.                            |
  | ``"auraHitsFriendly"`` | Specify whether the aura(s) hit other entities that are owned by  the player that owns the entity emitting the aura. |
  |  ``"auraHitsAllied"``  |     Specify whether the aura(s) hit other entities which belong to the same team as the entity emitting the aura.    |
  |   ``"auraHitsEnemy"``  |                                    Specify whether the aura(s) hit enemy entities.                                   |

  The values of the above fields MUST all be booleans

- The ``"auraTargetFilters"`` list specify requirements an entity needs to meet in order for it to
be affected by the aura(s). This MUST be an array of strings.
- The ``"auraTargetFiltersExclude"`` is the inverse of ``"auraTargetFilters"``: an entity MUST NOT
meet any of the requirements in order to be affected by the aura(s). This MUST be an array of strings.

#### 9.3.2. Aura Colour
The auras emitted by a modifier could have a *colour*. If an aura colour is specified, it will be 
displayed in a circle around the entity. The circle's size will be determined by the range of the
aura which has the highest range.

Aura colours are specified using the RGBA scheme, in an object. Example:
```jsonc
{
  // How much red.
  "r": 255,
  // How much green.
  "g": 180,
  // How much blue.
  "b": 30,
  // The transparency ratio. 1 is opaque and 0 is completely transparent.
  "a": 0.2 
}
```
The ``"r"``, ``"g"``, and ``"b"`` fields MUST only contain 8-bit *unsigned* integers--number
equal to or greater than 0, and smaller than 256.

The ``"a"`` field MUST be a floating-point number with a minimum of 0, and a maximum of 1 (both
bounds inclusive). 1 means the aura colour is to be completely opaque, and 0 means the aura
colour is to be completely transparent.

### 9.4. Changing Images when a Modifier is Applied
It is possible to change an entity's image/graphic effects while a modifier is being applied.
This is mostly useful for making entities appear as if they were burning (by applying a "fire" effect
to them or something else).

The ``"changeEntityImg"`` field could be set to ``true`` to give entities a new image when the
current modifier is applied. Entities which have the current modifier applied will have their image
changed to the graphic identified in the ``"entityImg"`` field.

If the ``"changeEntityImg"`` field is ``false``, the ``"entityImg"`` field MUST be ignored.

The ``"changeAtkEffect"`` and ``"atkEffect"`` fields work the same way as the ``"changeEntityImg"`` 
and ``"entityImg"`` fields, except they change the *attack effect* of the entity.

### 9.5. Playing Sounds when a Modifier is Applied
It is also possible to play sounds when a modifier is applied. The sound will be looped, with no
delay between plays of the sound.

The name of the sound-to-play could be specified in the ``"sound"`` field. The value of the
``"sound"`` field MUST be a string. If the sound requested does not exist, parsers SHOULD ignore it.

To set the volume of the sound-to-play, use ``"soundVolume"``. The value of ``"soundVolume"`` MAY be
a floating-point number, and it MUST be interpreted as a decimal-percentage. For example, the integer
``1`` means 100% of the sound's original volume, the decimal ``0.5`` means 50% of the sound's
original volume, and the decimal ``1.5`` means 150%.
