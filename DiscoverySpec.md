# Discover protocol specification

The discovery service is a server / service that runs at all times and knows of the existance of other services. The required protocol to talk to this server will be described here

## Message body

All messages, both sent and received, will have the same body.

```javascript
{
  payload: "", // a string representation of the data you are trying to send (in most cases a Javascript Object)
  signature: "" // a hex-representation of a SHA256 signature of the payload
}
```

When communicating with the Discovery Service, all `payload`s MUST include a `timestamp` field, which is a UNIX timestamp in seconds after the Epoch.

For `ack`s received by the client, the parsed `payload` will use the same format for each request:

```javascript
{
    success: true, // false if anything failed
    error: { // the error object is only present when success = false
      code: "unknownError"
    },
    // additional data
}
```

## Setting up the connection
1. Set up a Socket.io client that connects to `http://discovery.thesquare.me`
2. Send a `register` request to the server (see below)

## Global errors
- `packetInvalid`: The received packet is not an object
- `messageInvalid`: The received message is missing a payload or signature
- `payloadlInvalid`: The received payload is not a string of JSON data
- `notRegistered`: This service hasn't registered itself yet
- `signatureInvalid`: The received message has an invalid signature (could be tampered with)
- `unknownError`: An error has been handled but no specific response given
- `unexpectedError`: An error has not been handled properly

# Request methods

## `register`

Register the current service to the Discovery Service

**Arguments**
```javascript
{
  type: "api", // one of 'api', 'streaming' or 'chat'
  id: "" // the unique ID for this service (can not overlap ids of other services or types)
}
```

**ACK**
```javascript
{
  success: true,
  error: { // only present if success is false
    code: ""
  }
}
```

**Error codes**
- `typeMissing`: `type` is missing in the request body
- `idMissing`: `id` is missing in the request body
- `typeInvalid`: `type` is not a required type
- `alreadyRegistered`: `id` is already in use by another service

-----------------------

## `start`

Start the required services for a stream

**Arguments**
```javascript
{
  streamId: "", // the UUID of the stream that is starting
  streamer: "" // the user model of the person who is streaming
}
```

**ACK**
```javascript
{
  success: true,
  error: { // only present if success is false
    code: ""
  },
  data: { // only present if success is true
    chat: {}, // data from the chat server (like hostname)
    streaming: {} // data from the streaming server (like hostname and port)
  }
}
```

**Error codes**
- `streamIdMissing`: `streamId` is missing in the request body
- `streamer`: `streamer` is missing in the request body
- `noServers`: there are not enough servers online to fulfill this request
- `nodeTimeout`: the assigned servers did not respond in time

-----------------------

## `status`

Request status about all services

**Arguments**
```javascript
{
  // only timestamp (like all other requests)
}
```

**ACK**
```javascript
{
  success: true,
  error: { // only present if success is false
    code: ""
  },
  status: { // only present if success is true
    api: { // or 'streaming' or 'chat'
      status: "green", // status for all nodes of this type. "yellow" indicates error with some nodes and "red" means error with all nodes or no nodes present
      count: 0, // amount of nodes of this type
      nodes: [{
       id: "", // the ID of this specific node
       // other fields will be appended, depending on the type
      }]
    },
    streaming: {}, // similar to api
    chat: {} // similar to api
  }
}
```

**Error codes**
None at this point

# Received events

## `status`

This event requests status about the node

**Body**
```javascript
{
  // only timestamp like all other requests
}
```

**Expected ACK**
```javascript
{
  success: true,
  error: { // only if an error has been given
    code: ""
  },
  data: { // all info you want to send about your service
    status: "green", // a color indication about your node's health
    // all other data you want to send
  }
}
```

-----------------------

## `start`

This event requests the service to set up for a certain stream

**Body**
```javascript
{
  // same info as sent to the 'start' event
}
```

**Expected ACK**
```javascript
{
  success: true,
  error: { // only if an error has been given
    code: ""
  },
  data: { // all info that is required to connect to your service
    // id, hostname, port, etc
  }
}
```

-----------------------

## `stop`

This event requests the service to clean up for a certain stream

**Body**
```javascript
{
  // same info as sent to the 'stop' event
}
```

**Expected ACK**
```javascript
{
  success: true,
  error: { // only if an error has been given
    code: ""
  },
  data: { // only present if success is true
    // any data you might want to send after terminating a stream
  }
}
```
