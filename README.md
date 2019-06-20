# Crassus

The flexible multi channel extendable subscriber router for you and me.

## This repository (README)

This repository is the primary project management resource and is to be used for all documentation and feature requests. 

As Crassus is barely half way through its MVP development however no code should ever be entered into this repository. 

If you would to join along with another language (say for instance rust), please contact any member of the team to come in and join the fun!

The same for if you would like to join either of the go or csharp versions of project.

Or perhaps you are a technical writer ... then PLEASE, OH PLEASE say hello.

# Protocol

Beware you are entering a darkened room, you might be eaten by a Grue.

## Terminology

* Packet - This is used to describe a JSON data structure that is sent to and from Crassus
* Channel - A virtual identifier for a collection of subscribers
* Subscriber - A plugin that has associated with a channel for the purpose of data exchange, this may be on behalf of a Client
* Plugin - An extension to Crassus that offers a new interface to the core, Plugins may also offer extension functionality.
* Client - An end user-user that may be conneted to a Plugin via any kind of communication medium
* Protocol - A definition of a kind of json data structure to strictly adhere to

## Overview

This document is for the technical overlay of Crassus, describing the various standard types of Packet and what there use is. It should be the first document read before attempting to write any plugins or a Crassus of your own.

# Protocol

Crassus implements a split header/body structure in the form of a 2 element JSON array[2], these messages are transmitted and received via a WebSocket connection. Both datastructures present in the array[2] are of a standard type of Protocol.

The connection between the Plugin and the Server are always started with a handshake routine, after this packets may be both sent and recieved as designated to the channels the plugins are subscribed in.

## Protocol versions

All implementations of crassus should be able to handle at minimal this set of versions, even though it is expected extensions of them will come at a later time we believe having these present will ensure the highest compatability.

### Version 0
- uint version (native)
  - The version of the Protocol in array[1]
- UUID uuid (string or native)
  - A global unique identifier 

### Version 1
- uint[] crassus 
  - An array of uint's 
- 

## Handshake

The initial handshake is always sent first by the Plugin, it will always be of the design: [Protocol0,Protocol1], where Protocol1 will include a list of all protocols it supports.

The response by crassus will be of the format: [Protocol0,Protocol1] and will have a list with only one single element, stating which Protocol version should be used.