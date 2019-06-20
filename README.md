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
* UUID - See GUID
* GUID - An acronym for 'Globally Unique Identifier' (or 'Universally Unique Identifier'). It is a 128-bit integer number used to identify resources.

## Overview

This document is for the technical overlay of Crassus, describing the various standard types of Packet and what there use is. It should be the first document read before attempting to write any plugins or a Crassus of your own.

## Protocol

Crassus implements a split header/body structure in the form of a 2 element JSON array[2], these messages are transmitted and received via a WebSocket connection. Both datastructures present in the array[2] are of a standard type of Protocol.

The connection between the Plugin and the Server are always started with a handshake routine, after this packets may be both sent and recieved as designated to the channels the plugins are subscribed in.

### Protocol versions

All implementations of crassus should be able to handle at minimal this set of versions, even though it is expected extensions of them will come at a later time we believe having these present will ensure the highest compatability.

Also please note that while descriptions are added below each field, these are only included in these first packets for clarity.

#### Version 0
- name:version type:uint (native)
  - The version of the Protocol in array[1]
- name:uuid type:uuid (string or native)
  - A global unique identifier 

#### Version 1
- name:crassus type:uint[] 
  - An array of uint's 

#### Version 2-99 (future planning, PROPOSED)
The above two versions of the protocol are required for the initial handshake and are therefor absolutely stable, as we have an entire uint of version numbers the other 'core' crassus versions will start at 100. This is really a vanity request as obviously numbers in and of them self have no meaning here, but perhaps at some stage there may be a more intricate handshake required which obviously, would be in this reserved block.

#### Version 100

The first and likely most common packet, note italic means optional, bold means it is set by crassus and is not to be set by a sending plugin.

- name:crassus type:string[] 
- *name:option type:dictionary<string,string>*
  - name:report type:string
- name:routing type:dictionary<string,object>
  - *name:code type:int*
  - *name:code_extra type:string*
  - name:destination type:uuid
  - **name:source type:uuid**

* crassus: is where the primary packets of information will be kept in most cases it is imagined it will be yet more nested javascript (that will not be processed by crassus but just delivered to consumers)
* option: is where optional crassus features will be flagged
  * report: this is a request for a report to be sent back after a message is sent to crassus, the report types are as yet undefined but an example maybe 'delivery' where crassus will respond with a list of number of recipients of the message
* code: something to do with http codes, 200 etc I can't remember (HELP)
* code_extra: this was things like an alternative crassus to connect to for redirects etc and load balancing (HELP)
* destination: The object to send to (channel, plugin or crassus)
* source: Set by crassus, who sent the packet

### Plugin and channel identification

All plugins and channels are represented by GUID even though channels may have a textual representation or aliases, on subscription to one of them the response from crassus will always say what it's GUID is if .

### Handshake

The initial handshake is always sent first by the Plugin, it will always be of the design: [Protocol0,Protocol1], where Protocol1 will include a list of all protocols it supports.

The response by crassus will be of the format: [Protocol0,Protocol1] and will have a list with only one single element, stating which Protocol version should be used.

### Data stream

Send [Protocol0,ProtocolN] (as negotiated in the handshake) out to people in a channel.