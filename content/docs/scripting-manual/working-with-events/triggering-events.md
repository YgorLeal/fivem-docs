---
title: Triggering events
weight: 30
---

### Triggering local events
To trigger a server event from inside a server-side script, or trigger a client event from inside a client-side script, use the `TriggerEvent()` (or for JS, `emit()`) function.

#### Example
**Lua**
```lua
TriggerEvent("eventName", eventParam1, eventParam2)
```

**C#**
```csharp
TriggerEvent("eventName", eventParam1, eventParam2);
```

**JS**
```js
emit("eventName", eventParam1, eventParam2);
```

### Triggering server events
There are currently two different ways to trigger a server event from inside a **client** script.

For smaller transactions you should use `TriggerServerEvent`, while for larger transactions (which contain more data, multiple KBs and above) `TriggerLatentServerEvent` would be more optimal.

#### Example
**Lua**
```lua
TriggerServerEvent("eventName", eventParam1, eventParam2)
```

**C#**
```csharp
TriggerServerEvent("eventName", eventParam1, eventParam2);
```

**JS**
```js
emitNet("eventName", eventParam1, eventParam2);
```

#### Triggering latent server events
Latent events should be used when needing to transfer a large amount of data from client -> server, as latent events **do not** block the entire network channel, unlike `TriggerServerEvent`.

Latent events take an extra parameter 'bps' which stands for 'bytes per second', this defines how fast it should send data to the server. If 'bps' is set to -1 or 0 - default bps of 25000 will be applied.

**Lua**
```lua
TriggerLatentServerEvent("eventName", bps, eventParam1, eventParam2)
```

**C#**
```csharp
TriggerLatentServerEvent("eventName", bps, eventParam1, eventParam2);
```

**JS**
```js
TriggerLatentServerEvent("eventName", bps, eventParam1, eventParam2);
```

---

### Triggering client events
The same is applicable for triggering client events.

Client events use `TriggerClientEvent` and `TriggerLatentClientEvent` respectively, unlike server events you have to specify which users you want to send them to, or `-1` for all connected users.

**Lua**
```lua
-- Use -1 for "targetPlayer" if you want the event to trigger on all connected clients.
TriggerClientEvent("eventName", targetPlayer, eventParam1, eventParam2)
```

**C#**
```csharp
// Method one. Trigger an event directly on a client source.
player.TriggerEvent("eventName", eventParam1, eventParam2);

// Method two. Trigger an event for everyone on the server.
TriggerClientEvent("eventName", eventParam1, eventParam2);

// Method three. Triggering an event directly on a client source,
// but using the TriggerClientEvent function instead.
TriggerClientEvent(player, "eventName", eventParam1, eventParam2);
```

**JS**
```js
// Use -1 for "targetPlayer" if you want the event to trigger on all connected clients.
emitNet("eventName", targetPlayer, eventParam1, eventParam2);
```

#### Triggering latent client events
Latent events should be used when needing to transfer a large amount of data from server -> client, as latent events **do not** block the clients entire network channel, unlike `TriggerClientEvent`.

This is important for timeout functionality, as sending a large amount of data blocks the network for the client, and if blocked for too long, will result in the client timing out.

Latent events take an extra parameter 'bps' which stands for 'bytes per second', this defines how fast it should send data to the client. If 'bps' is set to -1 or 0 - default bps of 25000 will be applied.

'bps' applies to a single target. I.e. if `targetPlayer` is set to `-1` and event is sent to all connected users - the server will be effectively sending `bps * player_count` bytes every second.

##### Recommended bps values

| Use Case | Recommended bps | Notes |
|----------|-----------------|-------|
| Small data (< 10 KB) | 50,000 - 100,000 | Quick delivery without network strain |
| Medium data (10-100 KB) | 100,000 - 500,000 | Balance between speed and stability |
| Large data (100 KB - 1 MB) | 500,000 - 1,000,000 | Monitor server performance |
| Very large data (> 1 MB) | Consider chunking | Split into smaller events |

##### When to use latent events

Use latent events when your payload exceeds approximately **10-15 KB**. For reference:
- A simple table with player data: ~100-500 bytes
- - Inventory data with items: ~1-5 KB
  - - Large configuration tables: ~10-50 KB
    - - Map/world data: potentially several MB
     
      - Note: setting `bps` to extremely large numbers (approximately `10 000 000` and above) may lead connectivity and performance issues when large objects are sent. Because the system will try to send everything at once.
     
      - **Lua**
      - ```lua
        -- Use -1 for "targetPlayer" if you want the event to trigger on all connected clients.
        TriggerLatentClientEvent("eventName", targetPlayer, bps, eventParam1, eventParam2)
        ```

        **C#**
        ```csharp
        // Method one. Trigger an event directly on a client source.
        player.TriggerLatentEvent("eventName", bps, eventParam1, eventParam2);

        // Method two. Trigger an event for everyone on the server.
        TriggerLatentClientEvent("eventName", bps, eventParam1, eventParam2);

        // Method three. Triggering an event directly on a client source,
        // but using the TriggerLatentClientEvent function instead.
        TriggerLatentClientEvent(player, "eventName", bps, eventParam1, eventParam2);
        ```

        **JS**
        ```js
        // Use -1 for "targetPlayer" if you want the event to trigger on all connected clients.
        TriggerLatentClientEvent("eventName", targetPlayer, bps, eventParam1, eventParam2);
        ```
