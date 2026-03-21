---
sidebar_position: 3
---

# Server-Sent Events (SSE)

This tutorial explains how to use Server-Sent Events (SSE) in the project framework. SSE allows servers to push real-time updates to clients over HTTP.

---

## Introduction to SSE

Server-Sent Events (SSE) provide a way for servers to send automatic updates to clients via HTTP connections. Unlike WebSockets, SSE is unidirectional (server to client) and uses a simple event-stream format.

This framework supports SSE with decorators and services to easily create SSE-enabled controllers and send events.

---

## Defining SSE Controllers

Use the `@Controller` decorator to define controllers that handle SSE connections and events. You can specify a prefix and include sub-controllers.

Example from `user.ts` controller:

```typescript
@Controller({
  prefix: "user",
  controllers: [UserMetadata],
  interceptor: (data, req, res) => {
    return { data, intercepted: true };
  },
})
export class User {
  // Controller methods here
}
```

---

## Injecting SSE Service

Use the `@InjectSSE` decorator in controller methods to inject the SSE service. This service manages SSE connections and allows sending events.

Example usage:

```typescript
import { InjectSSE } from "@heliosjs/http";

@Controller("user")
export class User {
  async subscribe(@InjectSSE() sse) {
    const client = sse.createConnection(res);
    sse.sendToClient(client.id, {
      event: "welcome",
      data: { message: "Connected to SSE" },
    });
  }
}
```

---

## SSE Event Decorators

The framework provides decorators to handle SSE lifecycle events:

- `@OnSSEConnection()`: Called when a new SSE connection is established.
- `@OnSSEError()`: Called when an SSE error occurs.
- `@OnSSEClose()`: Called when an SSE connection closes.

Example from `user.ts`:

```typescript
import { OnSSEConnection, OnSSEError, OnSSEClose } from "@heliosjs/http";

@Controller("user")
export class User {
  @OnSSEConnection()
  async onsseconnection(@Request() req: any, @Response() res: any) {
    return req.body;
  }

  @OnSSEError()
  async onsseerror(@Request() req: any, @Response() res: any) {
    return req.body;
  }

  @OnSSEClose()
  async onsseclose(@Request() req: any, @Response() res: any) {
    return req.body;
  }
}
```

---

## Sending SSE Events

Use the injected SSE service to send events to clients. You can send events to specific clients by their ID.

Example:

```typescript
sse.sendToClient(clientId, {
  event: "eventName",
  data: { key: "value" },
});
```

---

## Enabling SSE Support in Server

To enable SSE support, configure your HTTP server with the `sse` option enabled and register your SSE controllers.

Example server setup:

```typescript
import { Server, Helios } from "@heliosjs/http";
import { User } from "./controllers/user";

@Server({
  controllers: [User],
  sse: { enabled: true },
})
class App {}

const server = new Helios(App);
server.listen().catch(console.error);
```

---

## Example Usage from `user.ts`

```typescript
import { Controller } from "@heliosjs/core";
import {
  InjectWS,
  IWebSocketService,
  OnSSEConnection,
  OnSSEError,
  OnSSEClose,
} from "@heliosjs/http";
import { IsString } from "class-validator";

class DTO {
  @IsString()
  id: string;
}

@Controller({
  prefix: "user",
  controllers: [],
  interceptor: (data, req, res) => {
    return { data, intercepted: true };
  },
})
export class User {
  @OnSSEConnection()
  async onsseconnection(@Request() req: any, @Response() res: any) {
    return req.body;
  }

  @OnSSEError()
  async onsseerror(@Request() req: any, @Response() res: any) {
    return req.body;
  }

  @OnSSEClose()
  async onsseclose(@Request() req: any, @Response() res: any) {
    return req.body;
  }
}
```

## Enabling SSE

To enable SSE in your Helios application, add the following configuration snippet to your module or application setup:

```ts
@Server({ sse: { enabled: true }} )
```

---

This tutorial provides a clear overview and practical examples to help you implement Server-Sent Events in your project using the provided decorators and services.

For more details, refer to the project README and source code examples.
