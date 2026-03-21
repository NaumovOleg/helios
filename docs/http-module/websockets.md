---
sidebar_position: 2
---

# WebSocket

This document provides detailed documentation for the WebSocket controller based on the example implementation found in `examples/controllers/socket.ts`.

## Overview

The `Socket` controller handles WebSocket connections and messages using decorators to define event handlers. It demonstrates how to manage client connections, subscribe to specific message types, and respond to WebSocket events.

## Enable websockets

```ts
@Server({
  controllers: [SocketController],
  websocket: { path: "/ws" },
})
export class App {}
```

## Decorators Explained

- `@Controller('socket')`: Defines a WebSocket controller with the base route or namespace `socket`.
- `@OnConnection()`: Marks a method to handle new WebSocket client connections.
- `@Subscribe('eventName')`: Marks a method to subscribe to a specific event type sent by clients.
- `@OnMessage('eventName')`: Marks a method to handle messages of a specific type.

## Socket Controller Methods

### onconnect(event: WebSocketEvent)

- Triggered when a new client connects.
- Sends a welcome message to the connected client.

### sunbcription(event: WebSocketEvent)

- Subscribes to the `chat` event.
- Checks the message content and ignores messages containing the word "bad".

### onPing(event: WebSocketEvent)

- Handles `ping` messages.
- Responds with a `ping` message containing the current server time.

### onChat(event: WebSocketEvent)

- Handles `chat` messages.
- Responds with an `onChat` message containing the current server time.

## Example Usage

```ts
import { Controller } from "@heliosjs/core";
import {
  OnConnection,
  OnMessage,
  Subscribe,
  WebSocketEvent,
} from "@heliosjs/http";

@Controller("socket")
export class Socket {
  @OnConnection()
  onConnect(event: WebSocketEvent) {
    return event.client.socket.send(
      JSON.stringify({ type: "welcome", data: { message: "welcome" } }),
    );
  }

  @Subscribe("chat")
  subscribeToChat(event: WebSocketEvent) {
    const msg = event.message?.data;
  }

  @OnMessage("ping")
  onPing(event: WebSocketEvent) {
    event.client.socket.send(
      JSON.stringify({ type: "ping", data: { time: Date.now() } }),
    );
  }

  @OnMessage("chat")
  onChat(event: WebSocketEvent) {
    event.client.socket.send(
      JSON.stringify({ type: "onChat", data: { time: Date.now() } }),
    );
  }
}
```

### Handling WebSocket Events

- When a client connects, the `onconnect` method sends a welcome message.
- Clients can send a `ping` message to receive the current server time.
- Clients can send a `chat` message; messages containing "bad" are ignored by the subscription handler.
- The `onChat` method responds to chat messages with a timestamp.

### Sending Messages Back to Clients

Messages are sent back to clients using the WebSocket `send` method on the client socket, with the message serialized as JSON. The message format includes a `type` field to identify the message type and a `data` field containing the payload.

Example:

```ts
event.client.socket.send(
  JSON.stringify({ type: "ping", data: { time: Date.now() } }),
);
```

This structure allows clients to handle different message types appropriately.
