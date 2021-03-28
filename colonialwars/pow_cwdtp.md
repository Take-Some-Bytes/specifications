# Protocol over WebSockets (POW): Colonial Wars Data Transfer Protocol
This document defines the CWDTP, or Colonial Wars Data Transfer Protocol, which is a
realtime communication protocol designed for use over WebSockets. All realtime communication
between Colonial Wars servers and clients must use this protocol.

Revision 2.

## 1. Conformance Requirements
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT",
"RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in
[RFC2119](https://tools.ietf.org/html/rfc2119).

Requirements phrased in the imperative as part of algorithms (such as "strip any leading space
characters" or "return false and abort these steps") are to be interpreted with the meaning
of the key word ("MUST", "SHOULD", "MAY", etc.) used in introducing the algorithm.

## 2. Opening Handshake
### 2.1. Transport-layer Handshake 
This protocol MUST be used over an existing transport-layer protocol, and it SHOULD be reliable.
Protocols like WebSockets or TCP are both acceptable.

The client MUST first complete the opening handshake for the transport-layer protocol of their
choice.

If the client is using WebSockets:
- The client MUST send a ``Sec-WebSocket-Protocol`` header with the value ``pow::cwdtp``.
- If the server does not respond with a ``Sec-WebSocket-Protocol`` header with the value ``pow::cwdtp``,
the client MUST abort the connection immediately.

### 2.2. Client Opening Message
Once the transport-layer connection is established, the client MUST send a JSON string, encoded in UTF-8,
that has the following structure:
```json
{
  "event": "cwdtp::client-hello",
  "meta": {
    "req_key": "<random 8 octet string in base64 format>"
  },
  "data": []
}
```
The ``data`` field MUST exist and it MUST be an empty JSON array. The value of the ``req_key`` field
in the ``meta`` object MUST be a string of 8 cryptographically random octets, encoded in base64 format.

### 2.3. Server Opening Message
After the server receives the ``cwdtp::client-hello`` message, the server is REQUIRED to reply to the client
as soon as possible with a JSON string. The JSON string MUST have the following format:
```json
{
  "event": "cwdtp::server-hello",
  "meta": {
    "res_key": "<response key in base64 format>",
    "cid": "<connection id>"
  },
  "data": []
}
```
The ``data`` field MUST exist and it MUST be an empty JSON array. The value of the ``res_key`` field
in the ``meta`` object MUST be a SHA-1 hash of the ``req_key`` sent by the client, combined with the
literal string ``"FJcod23c-aodDJf-302-D38cadjeC2381-F8fad-AJD3"``. The hash must be in base64 format.

#### 2.3.1 Connection ID
All connections MUST have an ID. This ID is sent along with the ``cwdtp::server-hello`` event. The ID
must be the value of the ``cid`` field in the ``meta`` object. The ID could be of any format and length,
but it is RECOMMENDED that the ID is at least 16 characters long.

### 2.4. Aborting the Handshake
To abort the handshake, an endpoint must send a ``cwdtp::close`` event with the ``error`` field
of the ``meta`` object set to true. Then, the endpoint that sent the close event MUST initiate
the closure of the transport-layer.

The client MUST abort the handshake if:
- A ``cwdtp::server-hello`` message is not sent within 30 seconds after the client sent its
``cwdtp::client-hello`` message.
- The ``res_key`` received does not equal the hash of the ``req_key`` combined with the literal string
``"FJcod23c-aodDJf-302-D38cadjeC2381-F8fad-AJD3"``.
- No connection ID is received in the ``cwdtp::server-hello`` message.

The server MUST abort the handshake if:
- A ``cwdtp::client-hello`` message is not sent within 30 seconds after the transport-layer connection.

Either client or server could abort the handshake if the message structure does not conform to
[Section 3](#3.-message-structure).

## 3. Message Structure
All messages transmitted in this protocol are REQUIRED to be in JSON format. Specifically, each message
must have the following structure:
```json
{
  "event": "<string>",
  "meta": {},
  "data": []
}
```
The ``event`` field must be a JSON string. The ``meta`` field MUST be a JSON object, and implementations
SHALL NOT put user data in the ``meta`` object. The ``data`` field MUST be a JSON array of arbitrary
data, sent by the user.

All messages must be transferred as UTF-16.

### 3.1. Transfer of Binary Data
To transfer an arbitrary sequence of binary data, it is RECOMMENDED to:
1. Convert the binary data into an array of octets, i.e. 8-bit unsigned integers.
2. Package the resulting array into a JSON object with the following structure:
    ```json
    {
      "binary": true,
      "type": "<name of data type>",
      "contents": [ "<contents of uint8array>" ]
    }
    ```
    The ``binary`` field is REQUIRED, and its value MUST be ``true`` or an equivalent. The ``type`` field
    is RECOMMENDED, so the recipent could convert the array of octets back into its original data type.
    The ``contents`` field is required, and it must contain the contents of the binary data as an
    array of octets.

3. Replace the occurance of the binary data with the object constructed in step 2.

## 4. Heartbeat Mechanism
The Colonial Wars Data Transfer Protocol uses the traditional ping/pong mechanism to ensure both sides
of the connection are still alive.

### 4.1. The cwdtp::ping Event
The ``cwdtp::ping`` event MUST only be sent by the server; if a server receives a ``cwdtp::ping`` event,
it MUST close the connection immediately. Ping events must not have any payload.

Upon receiving a ping, a client MUST send back a ``cwdtp::pong`` event as soon as possible.
A server SHOULD forcefully close the connection if a pong is not sent after a set amount of time.

### 4.2. The cwdtp::pong Event
The ``cwdtp::pong`` event MUST only be sent by a client; if a server receives a ``cwdtp::pong`` event,
it MUST close the connection immediately. Pong events must not have any payload.

The client MUST NOT send a pong without receiving a ping first.

## 5. Closing the Connection
### 5.1. Graceful Closure
To close the connection gracefully, an endpoint MUST send a ``cwdtp::close`` event.
The close event MUST include a reason why the connection was closed, in the ``meta``
object. The field name for the reason MUST be ``reason``, and the value of ``reason`` must be
a JSON string. If the connection was closed as a result of an error, the endpoint MUST specify
``error: true`` in the ``meta`` object.

The other endpoint, after receiving a ``cwdtp::close`` event, MUST send back a ``cwdtp::close-ack``
event; no payload is allowed, and the connection is considered closed.

The endpoint that initiated the closure must then initiate the transport-layer connection closure.
If the transport-layer connection is not closed within 30 seconds, the endpoint that received the
``cwdtp::close`` event MAY forcefully close the transport-layer connection.

### 5.2. Forceful Closure
To forcefully close the connection, an endpoint SHOULD just terminate the transport-layer
connection.

## 6. Reserved Event Names and their Meanings
The following event names are reserved, and cannot be used by end-users. An error SHOULD be thrown
if an user attempted to emit a reserved event.
- ``cwdtp::client-hello``: The opening handshake event for the client.
- ``cwdtp::server-hello``: The opening handshake event for the server.
- ``cwdtp::ping`` and ``cwdtp::ping``: Heartbeat events.
- ``cwdtp::close``: Connection closure initiator.
- ``cwdtp::close-ack``: Connection closure acknowledgement. Neither endpoint could send events
after this event.
