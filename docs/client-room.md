# Room API (Client-side)

## Methods

### `listen (path: string, callback: Function)`

Listen to room state changes from the room handler.

Use this method to synchronize the room state from the server with the clients.

#### Examples

##### Listening to map data structures

```typescript fct_label="JavaScript"
room.listen("players/:id", (change) => {
  if (change.operation === "add") {
    console.log("new player added to the state");
    console.log("player id:", change.path.id);
    console.log("player data:", change.value);

  } else if (change.operation === "remove") {
    console.log("player has been removed from the state");
    console.log("player id:", change.path.id);
  }
});
```

```csharp fct_label="C#"
using Colyseus;
// ...

room.Listen("players/:id", OnPlayerChange);

void OnPlayerChange (DataChange change)
{
  if (change.operation == "add") {
    Debug.Log ("new player added to the state");
    Debug.Log (change.path["id"]);
    Debug.Log (change.value);

  } else if (change.operation == "remove") {
    Debug.Log ("player has been removed from the state");
    Debug.Log (change.path["id"]);
  }
});
```

```lua fct_label="lua"
room:listen("players/:id", function(change)
  if (change.operation == "add") then
    print("new player added to the state")
    print(change.path["id"])
    print(change.value)

  elseif (change.operation == "remove") then
    print("player has been removed from the state")
    print(change.path["id"])
  end
end)
```

```haxe fct_label="Haxe"
room.listen("players/:id", function (change) {
  if (change.operation === "add") {
    trace("new player added to the state");
    trace("player id:" + change.path.id);
    trace("player data:" + Std.string(change.value));

  } else if (change.operation === "remove") {
    trace("player has been removed from the state");
    trace("player id:" + change.path.id);
  }
});
```

##### Listening to attribute changes of deep data structures

```typescript fct_label="JavaScript"
room.listen("players/:id/:attribute", (change) => {
  console.log(change.operation); // => "add" | "remove" | "replace"
  console.log(change.path.attribute, "has been changed");
  console.log(change.path.id);
  console.log(change.value);
});
```

```csharp fct_label="C#"
using Colyseus;
// ...

room.Listen("players/:id/:attribute", OnPlayerAttributeChange);

void OnPlayerAttributeChange (DataChange change)
{
  Debug.Log (change.operation); // => "add" | "remove" | "replace"
  Debug.Log (change.path["attribute"] + "has been changed");
  Debug.Log (change.path["id"]);
  Debug.Log (change.value);
});
```

```lua fct_label="lua"
room:listen("players/:id/:attribute", function()
  print(change.operation) // => "add" | "remove" | "replace"
  print(change.path["attribute"] + "has been changed")
  print(change.path["id"])
  print(change.value)
end)
```

```haxe fct_label="Haxe"
room.listen("players/:id/:attribute", function(change) {
  trace(change.operation); // => "add" | "remove" | "replace"
  trace(change.path.attribute + " has been changed");
  trace(change.path.id);
  trace(Std.string(change.value));
});
```

!!! Tip
    See [State synchronization](client-state-synchronization) for more examples on how to use the `listen` method.

### `send (data)`

Send message to the room handler.

```typescript fct_label="JavaScript"
room.send({ move: "left" });
```

```csharp fct_label="C#"
room.Send(new { move = "left" });
```

```lua fct_label="lua"
room:send({ move = "left" })
```

```haxe fct_label="Haxe"
room.send({ move: "left" });
```

Use [Room#onMessage()](api-room/#onmessage-client-data) from the server-side to read the message.

### `leave ()`

Disconnect from the room.

```typescript fct_label="JavaScript"
room.leave();
```

```csharp fct_label="C#"
room.Leave();
```

```lua fct_label="lua"
room:leave()
```

```haxe fct_label="Haxe"
room.leave();
```

!!! Tip
    Use [Room#onLeave()](api-room/#onleave-client-consented) to handle the disconnection from the server-side.

### `removeAllListeners()`

Remove all event and data listeners.

## Properties

### `id: string`

The unique idenfitier of the room. You can share this id with other clients in
order to allow them to connect directly to this room.

```typescript fct_label="JavaScript"
// get `roomId` from the query string
let roomId = location.href.match(/roomId=([a-zA-Z0-9\-_]+)/)[1];

// connect the client directly into a specific room id
let room = client.join(roomId);
```

!!! Warning
    If you're looking for the unique identifier of the client, use [`client.id`](client-overview/#id-string) instead.

### `sessionId: string`

Unique session identifier.

This property matches the [`client.sessionId`](api-client/#sessionid-string) from the server-side.

### `name: string`

Name of the room handler. Ex: `"battle"`.

### `state: any`

The current room's state. This variable is always synched with the latest
`state` from the server-side. To listen for updates on the whole state, see
[`onStateChange`](#onstatechange) event.

## Events

### onStateChange

This event is triggered when the server updates its state.

```typescript fct_label="JavaScript"
room.onStateChange.addOnce(function(state) {
  console.log("this is the first room state!", state);
});

room.onStateChange.add(function(state) {
  console.log("the room state has been updated:", state);
});
```

```csharp fct_label="C#"
room.OnStateChange += (object sender, RoomUpdateEventArgs e) => {
  if (e.isFirstState) {
    Debug.Log ("this is the first room state!");
  }

  Debug.Log ("the room state has been updated");
}
```

```lua fct_label="lua"
room:on("statechange", function(state)
  print("new state:", state)
end)
```

```haxe fct_label="Haxe"
room.onStateChange = function(state) {
  trace("new state:" + Std.string(state));
};
```

### onMessage

This event is triggered when the server sends a message directly to the client.

```typescript fct_label="JavaScript"
room.onMessage.add(function(message) {
  console.log("server just sent this message:");
  console.log(message);
});
```

```csharp fct_label="C#"
room.OnMessage += (object sender, MessageEventArgs e) => {
  Debug.Log ("server just sent this message:");
  Debug.Log(e.message);
}
```

```lua fct_label="lua"
room:on("message", function(message)
  print("server just sent this message:")
  print(message)
end)
```

```haxe fct_label="Haxe"
room.onMessage = function(message) {
  trace("server just sent this message:");
  trace(Std.string(message));
};
```

!!! Tip
    To send a message from the server directly to the clients you'll need to use
    either [room.send()](api-room/#send-client-message) or
    [room.broadcast()](api-room/#broadcast-message)

### onJoin

This event is triggered when the client successfuly joins the room.

```typescript fct_label="JavaScript"
room.onJoin.add(function() {
  console.log("client joined successfully");
});
```

```csharp fct_label="C#"
room.OnJoin += (object sender, EventArgs e) => {
  Debug.Log ("client joined successfully");
}
```

```lua fct_label="lua"
room:on("join", function()
  print("client joined successfully")
end)
```

```haxe fct_label="Haxe"
room.onJoin = function () {
  trace("client joined successfully");
};
```

### onLeave

This event is triggered when the client leave the room.

```typescript fct_label="JavaScript"
room.onLeave.add(function() {
  console.log("client left the room");
});
```

```csharp fct_label="C#"
room.OnLeave += (object sender, EventArgs e) => {
  Debug.Log ("client left the room");
}
```

```lua fct_label="lua"
room:on("leave", function()
  print("client left the room")
end)
```

```haxe fct_label="Haxe"
room.onLeave = function () {
  trace("client left the room");
};
```

### onError

This event is triggered when some error occurs in the room handler.

```typescript fct_label="JavaScript"
room.onError.add(function(err) {
  console.log("oops, error ocurred:");
  console.log(err);
});
```

```csharp fct_label="C#"
room.OnError += (object sender, EventArgs e) => {
  Debug.Log ("oops, error ocurred:");
  Debug.Log(e);
}
```

```lua fct_label="lua"
room:on("error", function()
  print("oops, error ocurred:")
  print(e)
end)
```

```haxe fct_label="Haxe"
room.onError = function(err) {
  trace("oops, error ocurred:");
  trace(err);
};
```
