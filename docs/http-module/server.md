---
sidebar_position: 1
---

# HTTP Server

The HTTP Server module in HeliosJS allows you to create and configure an HTTP server using the `@Server` decorator and the `HttpServer` class. This documentation explains how to use the `@Server` decorator to configure your server, the available configuration options, and how to instantiate and run the server using the `HttpServer` class.

## Using the `@Server` Decorator

The `@Server` decorator is a class decorator that configures the HTTP server with various options such as port, host, controllers, middlewares, CORS, WebSocket, SSE, GraphQL, and more.

### Basic Usage

```ts
import { Server } from '@heliosjs/http';

@Server({
  port: 3000,
  controllers: [...],
  middlewares: [...],
  cors: {...},
  websocket: { path: '/ws' },
  sse: { enabled: true },
  graphql: { path: '/graphql', resolvers: [...], pubSub },
  // other options
})
class MyServer {}
```

### Example from `packages/src/examples/app.ts`

```ts
import { ANY, Controller, Req } from "@heliosjs/core";
import { Server } from "@heliosjs/http";
import path from "path";

import { UserResolver, pubSub } from "./controllers/resolver";
import { Socket } from "./controllers/socket";
import { User } from "./controllers/user";

@Controller({
  prefix: "api",
  controllers: [User, Socket],
  middlewares: [function Global(req, res, next) {}],
})
export class Root {
  @ANY()
  use() {
    return "default";
  }
}

@Controller({ prefix: "metric", controllers: [] })
export class MetricsController {
  @ANY()
  async any(@Req() resp: any) {}
}

@Server({
  controllers: [Root, Socket, MetricsController],
  statics: [
    { path: path.join(__dirname, "../../"), options: { index: "index.html" } },
  ],
  // websocket: { path: '/ws' },
  graphql: { path: "/graphql", resolvers: [UserResolver], pubSub },
  sse: { enabled: true },
})
export class App {}
```

## Creating and Running the Server

To create and run the HTTP server, use the `HttpServer` class and pass your decorated server class to it.

### Example from `packages/src/examples/server.ts`

```ts
import { Helios } from "@heliosjs/http";
import { App } from "./app";

const server = new Helios(App);
server.listen().catch(console.error);
```

## `@Server` Decorator Configuration Options

The `@Server` decorator accepts a configuration object with the following properties:

| Property       | Type                                                                              | Description                                                                       |
| -------------- | --------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `port`         | `number`                                                                          | Port number the server listens on.                                                |
| `host`         | `string`                                                                          | Hostname or IP address to bind the server.                                        |
| `controllers`  | `ControllerType[]`                                                                | Array of controller classes to handle routes.                                     |
| `middlewares`  | `MiddlewareCB[]`                                                                  | Array of middleware callback functions to apply globally.                         |
| `cors`         | `CORSConfig`                                                                      | Configuration object for Cross-Origin Resource Sharing (CORS).                    |
| `interceptor`  | `InterceptorCB`                                                                   | Interceptor callback function for request/response interception.                  |
| `errorHandler` | `(error: AppError, req: Request, response: Response) => any`                      | Custom error handling callback.                                                   |
| `sanitizers`   | `SanitizerConfig[]`                                                               | Array of sanitizer configurations to sanitize request data.                       |
| `statics`      | `StaticConfig[]`                                                                  | Array of static file serving configurations.                                      |
| `websocket`    | `{ path: string; lazy?: boolean }`                                                | WebSocket configuration with path and optional lazy loading.                      |
| `sse`          | `{ enabled: boolean }`                                                            | Server-Sent Events configuration to enable SSE support.                           |
| `graphql`      | `{ path: string; playground?: boolean; pubSub?: PubSub; resolvers?: Function[] }` | GraphQL configuration including endpoint path, playground, pubSub, and resolvers. |

## Summary

- Use the `@Server` decorator to configure your HTTP server class.
- Pass the decorated class to `Helios` to create and run the server.
- Configure controllers, middlewares, CORS, WebSocket, SSE, GraphQL, and other options via the decorator.

This setup provides a flexible and powerful way to build HTTP servers with HeliosJS.
