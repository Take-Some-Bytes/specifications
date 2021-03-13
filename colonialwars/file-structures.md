# Save File Structures
Structures of Colonial Wars save files.

## Map Save File structure
Structure of Colonial Wars map save files.
```jsonc
{
  "configType": "game-config", // Must be "game-config"
  "meta": {
    "name": "Valley", // Must only have alphanumerical characters and spaces
    "mode": "Teams", // Either Teams, KOTH, or Siege
    "maxPlayers": 40, // Must be a positive integer
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
        "name": "British", // Must be a string with alphanumeric characters.
        "spawnPosition": { "x": 100, "y": 100 }, // Must have an x and y axis.
        "description": "The British." // A small description of the team.
        // Must be less than 150 characters.
      },
      {
        "name": "French",
        "spawnPosition": { "x": 5900, "y": 5900 },
        "description": "The French."
      }
    ],
    "tileType": "grass", // Must be rock, grass, or sand.
    "defaultHeight": 0 // Must be 0, 1, or 2
  }
}
```
