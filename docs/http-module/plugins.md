---
sidebar_position: 11
---

# Custom Plugins

This section explains how to create custom plugins to extend the framework with your own logic. Plugins allow you to hook into the request lifecycle and add reusable functionality for both HTTP servers and AWS Lambda adapters.

## What is a Plugin?

A plugin is an object that implements lifecycle hooks and HTTP hooks to run custom code at specific points during request processing. Plugins can be used to add features like metrics, logging, database connection management, authentication, and more.

## Plugin Structure

A plugin is a plain object with the following properties:

- `name` (string): A unique name for the plugin.
- `hooks` (optional object): Contains HTTP lifecycle hook functions:
  - `beforeRequest` (optional): Runs before each raw HTTP request is processed. Receives the Node.js `IncomingMessage` object.
  - `beforeRoute` (optional): Runs before routing the request. Receives the framework `Request` and `Response` objects.
  - `afterResponse` (optional): Runs after the response is sent. Receives the framework `Request` and `Response` objects.
- Lifecycle hooks (all optional):
  - `onInit(server)`: Called when the server initializes.
  - `onStart(server)`: Called when the server starts listening.
  - `onStop(server)`: Called when the server stops.
- `middleware` (optional): A middleware callback function.

Example plugin skeleton:

```typescript
import { IncomingMessage, Server } from "http";
import { MiddlewareCB, Request, Response } from "@heliosjs/core";
import { Plugin } from "@heliosjs/http";

const myPlugin: Plugin = {
  name: "myPlugin",
  onInit(server) {},
  onStart(server) {},
  onStop(server) {},

  middleware(req, res, next) {
    next();
  },

  hooks: {
    beforeRequest(req: IncomingMessage) {},
    beforeRoute(req: Request, res: Response) {},
    afterResponse(req: Request, res: Response) {},
  },
};
```

## Using Plugins with HTTP Server

To use a plugin with an HTTP server:

1. Create your plugin object.
2. Register the plugin with the server instance using `usePlugin()`.

Example:

```typescript
import { Server, Helios } from "@heliosjs/http";
import { User, UserMetadata } from "./controllers";
import { Plugin } from "@heliosjs/http";

@Server({ controllers: [User, UserMetadata] })
class App {}

const server = new Helios(App);

server.usePlugin(metricsPlugin);

server.listen().catch(console.error);
```
