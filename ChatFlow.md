# Chat protocol specification

To interact with other viewers or streamers, we have the ChatService. The required protocol will be described here

## Message body

All messages, both sent and received, will have the same body.

```javascript
{
  payload: "", // a string representation of the data you are trying to send (in most cases a Javascript Object)
  signature: "" // a hex-representation of a SHA256 signature of the payload
}
```

For `event`s or `ack`s received by the client, the parsed `payload` will use the same format for each request:

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

1. Get the right ChatService node for the stream you are watching (`GET /v1/streams/:streamid`)
2. Set up a Socket.io client that connects with the right `hostname`
3. Send a `identify` request to the server (see below)
4. Send a `join` request per `room` you want to chat in

## Global errors
- `packetInvalid`: The received packet is not an object
- `messageInvalid`: The received message is missing a `payload` or `signature`
- `payloadlInvalid`: The received `payload` is not a `string` of `JSON` data
- `notIdentified`: This socket hasn't successfully executed a `identify` request
- `signatureInvalid`: The received message has an invalid signature (could be tampered with)

# Request methods

## `identify`

Identify is the event (method) used to identify yourself as an user to the server.

**Arguments**
```javascript
{
  publicKey: "" // a base64 encoded RSA PEM public key (thus: the Base64 encode of the PEM string starting with "------ BEGIN" etc)
}
```

**ACK**
```javascript
{
  success: true,
  error: { // only present if success is false
    code: "unknownError"
  }
}
```

If an user is not identified, further messages can not be sent

**Error codes**
- `alreadyIdentified`: This connection has already identified itself
- `publicKeyMissing`: There is no `publicKey` parameter sent in the body
- `userNotFound`: There is no user to be found with the supplied `publicKey`

------------------

## `join`

Join a specific `room` (each `stream` has it's own `room`)

**Arguments**
```javascript
{
  room: "" // a UUID that belongs to the stream you are trying to watch
}
```

**ACK**
```javascript
{
  success: true,
  error: { // only present if success is false
    code: "unknownError"
  },
  room: { // only present if success is true
    title: "", // stream title
    streamer: {
      // user object containing the streamer's info
    }
  }
}
```

**Error codes**
- `roomMissing`: There is no `room` parameter sent in the body
- `roomInvalid`: The `room` parameter is not a valid `stream.id` or the `stream` uses another server

------------------

## `message`

Send a message to a specific `room`

**Arguments**
```javascript
{
  room: "", // a UUID that belongs to the stream you are trying to watch
  message: "hello world" // a string message that you are trying to send to other viewers
}
```

**ACK**
```javascript
{
  success: false, // only sends an `ack` if an error occurred
  error: {
    code: "unknownError"
  }
}
```

**Error codes**
- `roomMissing`: There is no `room` parameter sent in the body
- `roomInvalid`: The `room` parameter is not a valid `stream.id` or the `stream` uses another server
- `chatMessageInvalid`: The sent message is missing or not a string
- `notInRoom`: The user has not `join`ed the `room` it is trying to send in

# Received events

## `closing room`

This event is received when the server closes a room

**Body**
```javascript
{
  room: "", // the UUID belonging to this room
  reason: "" // a string containing the reason (if any, else undefined or null)
}
```

## `message`

An incoming chat message. These events are signed TWICE, once by the sender, once by the server. Some data is contained in the first `payload`, some in the 2nd

**Body**
```javascript
{
  sender: "", // UUID of the sender
  room: "", // UUID of the room
  data: { // the original message as received by the server, for ease of reading, already verified and 'unpacked' in this example
    room: "", // the UUID of the room the sender wanted to send to
    message: "" // the message sent by the sender
  }
}
```

# Verify validity of sender messages

To make sure that the message you received was sent by the sender, you first have to retrieve the user's `publicKey`. To do so, you can request `GET http://api.thesquare.me/v1/users/:userid`, who's response will also return the `publicKey` for this user in Base64 format. It might be smart to cache these details, so that user info doesn't have to be requested every time a chat message is received
