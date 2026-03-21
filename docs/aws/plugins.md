---
sidebar_position: 6
---

# Custom Plugins for AWS Lambda

This section explains how to create and use custom plugins specifically for the AWS Lambda adapter. Plugins allow you to hook into the Lambda event lifecycle and add reusable functionality such as logging, metrics, database connection management, and more.

## Lambda Plugin Interface

A Lambda plugin is an object implementing the following interface:

```typescript
export interface Hooks {
  beforeRequest?: (req: IncomingMessage) => void | Promise<void>;
  beforeRoute?: (req: Request, response: Response) => void | Promise<void>;
  afterResponse?: (req: Request, res: Response) => void | Promise<void>;
}

export type PluginHookKeys = keyof Hooks;

export interface Plugin {
  name: string;
  onInit?(
    app: ILambdaAdapter,
    event: LambdaEvent,
    context: Context,
  ): void | Promise<void>;
  hooks?: Hooks;
}
export type PluginKeys = keyof Omit<Plugin, "name" | "hooks">;
```

### Explanation

- `name`: Unique plugin name.
- `onInit`: Lifecycle hook called once when the Lambda adapter initializes. Receives the Lambda adapter instance, the Lambda event, and the Lambda context.
- `hooks`: Object containing HTTP lifecycle hooks:
  - `beforeRequest`: Called before the raw HTTP request is processed. Receives the Node.js `IncomingMessage`.
  - `beforeRoute`: Called before routing the request. Receives the framework `Request` and `Response` objects.
  - `afterResponse`: Called after the response is sent. Receives the framework `Request` and `Response` objects.

## Using Plugins with AWS Lambda Adapter

You can use plugins to extend the Lambda adapter with custom logic. Register plugins using the `usePlugin()` method on the Lambda adapter instance.

### Example Plugin

```typescript
import { Plugin } from "quantum-flow/aws";

const dbConnectionPlugin: Plugin = {
  name: "dbConnection",
  hooks: {
    beforeRequest: async (req) => {},
  },
  onInit: async (app, event, context) => {},
};
```

### Registering Plugin with Lambda Adapter

```typescript
import { LambdaAdapter } from "quantum-flow/aws";
import { Root } from "./controllers";

const lambdaAdapter = new LambdaAdapter(Root);
lambdaAdapter.usePlugin(dbConnectionPlugin);

export const handler = lambdaAdapter.handler;
```

## Plugin Hooks Lifecycle

| Hook            | When it runs                       | Arguments                                                       |
| --------------- | ---------------------------------- | --------------------------------------------------------------- |
| `onInit`        | When Lambda adapter initializes    | `app: ILambdaAdapter`, `event: LambdaEvent`, `context: Context` |
| `beforeRequest` | Before raw HTTP request processing | `req: IncomingMessage`                                          |
| `beforeRoute`   | Before routing the request         | `req: Request`, `res: Response`                                 |
| `afterResponse` | After response is sent             | `req: Request`, `res: Response`                                 |

## Example Usage with Lambda Adapter

### Plugin Structure

```typescript
import { IncomingMessage } from "http";
import { Request, Response } from "quantum-flow/core";
import { Plugin } from "quantum-flow/aws";

const examplePlugin: Plugin = {
  name: "examplePlugin",
  hooks: {
    beforeRequest(req: IncomingMessage) {
      console.log("Before raw request");
    },

    beforeRoute(req: Request, res: Response) {
      console.log("Before routing");
    },

    afterResponse(req: Request, res: Response) {
      console.log("After response");
    },
  },

  onInit(app, event, context) {
    console.log("Lambda adapter initialized");
  },
};
```

## Best Practices for Plugin Development

- Keep plugins focused on a single responsibility.
- Use async hooks for any I/O or setup operations.
- Avoid blocking the request lifecycle.
- Use the plugin `name` to identify and manage plugins.
- Test plugins independently.

## Summary

Plugins provide a powerful way to extend the AWS Lambda adapter and HTTP server with reusable logic that integrates seamlessly into the request lifecycle.

Use the plugin API to hook into lifecycle events and add features like metrics, logging, and connection management.

---
