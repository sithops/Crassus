# Protocol Version 0

Beware you are entering a darkened room, you might be eaten by a Grue.

## Overview

This RFC describes Crassus message structure and interaction protocol.

## Terminology

Channel		A virtual identifier for a collection of subscribers.

Client 		An end user-user that may be conneted to a Plugin via any kind 
		of communication medium.

Message		This is used to describe a JSON data structure that is sent to 
 		and from Crassus. Messages must adhere to exactly one version
		of the Crassus protocol and may not attempt to mix versions.

Plugin		An extension to Crassus that offers a new interface to the 
		core. Plugins may also offer extension functionality.


Protocol 	A definition of a kind of json data structure to strictly 
		adhere to.

Subscriber	A plugin that has associated with a channel for the purpose of
 		data exchange, this may be on behalf of a Client.


# Protocol

Crassus implements a split header/body structure in the form of a 2 element
JSON array[2], these messages are transmitted and received via a WebSocket 
connection. Both datastructures present in the array[2] are of a standard type 
of protocol.

The connection between the Plugin and the Server are always started with a
handshake routine, after this packets may be both sent and recieved as
designated to the channels the plugins are subscribed in.

## Handshakes and Initialization

Over time the Crassus protocol will evolve. In order to ensure the plugins and 
server agree on verion type, all connections must be initialized and negotiate
a protocol version first.

### Initialization Flow

The initialization flow is designed to be "frozen" and is guaranteed to be
compatible with all future versions of the Crassus procotol.

The plugin or client initiates a connection via Websocket to the Crassus server
and sends a JSON-encoded message in the following format:

```
[0,1,2]
```

Where the elements are `uint`s advertising the versions the client wishes to
use. The server will respond with a uint array containing either 0 or 1
elements. An empty array indicates the server has no compatible versions and
the connection will terminate. An array with one element indicates the
negotiated version.

Example responses:
```
[1]
```

This indicates a negotiated version of protocol v1. 

```
[]
```

This indicates the server has no compatible versions.

### Version 0
Example Message:
```
[
 {
   version: INT
   UUID: uuid
 } 
{
   "Crassus": [
      "COMMAND",
      "TARGET",
      "?DATA?"
   ],
   "Option": {
      "report": "INT/Report type"
   },
   "Routing": {
      "code": "INT/2323",
      "code_extra": "STRING/Not sure about this one, but if we need to give more info?",
      "destination": "UUID",
      "source": "UUID"
    }
  }
]
```
