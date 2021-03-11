# Colonial Wars Message Structures
Definition of message structures that are sent over the network for Colonial Wars.

## HTTP Response Body Structure
Standard structure of HTTP response body in JSON.
Revision 1.
```jsonc
{
  "status": "ok", // Must be "ok" or "error".
  "error": {}, // Must be a non-empty object for "error" statuses.
  "data": {}, // Must contain the data for the response.
}
```

## Realtime Message Structure
Standard structure of a websocket/long-polling message. Meant for real-time updates
and messages. Revision 1.
```jsonc
{
  "status": "ok", // Must be "ok" or "error".
  "event": "game-update", // Must be a string.
  "conn-id": "", // An ID for this connection.
  // Should be a string (though numbers are also allowed).
  "error": {}, // Must be a non-empty object for "error" statuses.
  "data": {}, // Must contain the data for the message.
}
```
