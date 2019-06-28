# Protocol Version 0

Beware you are entering a darkened room, you might be eaten by a Grue.

## Overview

This RFC describes Crassus message structure and interaction protocol version
0.

## Terminology

Channel		A virtual identifier for a collection of subscribers.

Client 		An end user that may be conneted to a Plugin via any kind 
		of communication medium.

Message		This is used to describe a JSON data structure that is sent to 
 		and from Crassus. Messages must adhere to exactly one version
		of the Crassus protocol and may not mix versions.

Plugin		An extension to Crassus that offers a new interface to the 
		core. Plugins may also offer extension functionality.

Protocol 	Definition or spec of JSON data format to which message
		exchanges must adhere.

Subscriber	A plugin that has associated with a channel for the purpose of
 		data exchange, this may be on behalf of a Client.


## Protocol Format

The following describes the format of Crassus messages, connection, and
operation.

### Message Body

Crassus implements a split header/body structure in the form of a 2-element
JSON array. These messages are transmitted and received via a WebSocket 
connection. Both structures present in the array are defined by the
protocol itself.

The connection between the Plugin and the Server are always started with a
handshake routine which negotiates the version the Plugin and Ser will use. 
After this packets may be both sent and recieved per the spec of the given
protocol agreed to in the handshake.

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

Where each element is a `uint` advertising the versions the client wishes to
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

For this document, a crassus message will be referred to as `m`. 

When possible, all Crassus message versions will be a JSON array of two 
elements. `m[0]` will be the header and `m[1]` will be the body.

#### V0 Header

| Key     | Type   | Required | Description                   | 
|---------|--------|----------|-------------------------------|
| version | int    | yes      | Negotiated protocol version   |
| uuid    | string | yes      | Sender-generated message UUID |


#### V0 Body

| Key     | Type   | Required | Description                                   | 
|---------|--------|----------|-----------------------------------------------|
| body    | string | yes      | base64 representation of actual message       |
|---------|--------|----------|-----------------------------------------------|
| routing | hash   | yes      | Hash representation of source and destination | 
| option  | hash   | no       | Control messages for plugins                  |
| tag     | string | no       | Optional free form text field                 |

#### V0 Routing Hash

| Key         | Type   | Required | Description                                   | 
|-------------|--------|----------|-----------------------------------------------|
| source      | string | yes      | UUID of sending plugin/client.                | 
| destination | string | yes      | Message destination. May be UUID or plugin ID | 

#### V0 Option Hash

Free-form hash that plugins use to pass control messages outside of raw data.
May be used to indicate if a reply should be unicast, a report should be
generated, or anything else. Plugins are responsible for documenting their own
usage of the `options` hash.

| Key         | Type     | Required | Description                                   | 
|-------------|----------|----------|-----------------------------------------------|
|             | unsigned |          | If set, which report to send defaults to 0 if |
| report      | integer  | no       | set erronously or present but not set.

##### Report Types

| Key         |  Description                                                       | 
|-------------|--------------------------------------------------------------------|
| 0           | Send back a report that includes details of who the message was    |
|             | sent to 														   |

## Guid/UUID / Object identificaition

All plugins, packets and functions are uniquely identified by a UUID, they are generated in the following places:
* Plugin - Generated by Crassus sent to the Plugin after version negotiation.
* Function - TBA
* Packet - Generated by the plugin