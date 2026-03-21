---
sidebar_position: 4
---

# Middleware

Middleware functions are functions that have access to the request and response objects. They can execute code, modify requests/responses, end the request-response cycle, or call the next middleware in the stack.

## What is Middleware?

Middleware in HeliosJS works similarly to Express/Koa but with a decorator-based approach. Each middleware can:

- Execute any code
- Modify the request or response
- End the request-response cycle
- Call the next middleware in the stack

## Middleware Annotations in HeliosJS

HeliosJS provides several middleware-related annotations (decorators) to facilitate middleware handling in a declarative way. These annotations are found in the `middlewares` package and include:

### @Cors

The `@Cors` decorator configures Cross-Origin Resource Sharing (CORS) settings for routes or controllers. It accepts a configuration object to specify allowed origins, methods, headers, and other CORS options.

```typescript
import { Cors } from "@heliosjs/middlewares";
import { Controller, Get } from "@heliosjs/core";

@Controller("api")
@Cors({
  origin: "*",
  methods: ["GET", "POST"],
  allowedHeaders: ["Content-Type", "Authorization"],
})
export class ApiController {
  @GET("ping")
  @Cors({
    origin: "*",
    methods: ["GET", "POST"],
    allowedHeaders: ["Content-Type", "Authorization"],
  })
  ping() {}
}
```

### @Sanitize

The `@Sanitize` decorator applies sanitization rules to incoming request data. It accepts a sanitizer configuration or an array of configurations to clean and validate inputs.

```typescript
import { Sanitize } from "@heliosjs/middlewares";
@Sanitize({
  schema: Joi.object({ name: Joi.string().trim().min(2).max(50).required() }),
  action: "both",
  options: { abortEarly: false },
  stripUnknown: true,
  type: "body",
})
@Controller("api")
export class CorsMiddleware {
  @Get("ping")
  @Sanitize({
    schema: Joi.object({ name: Joi.string().trim().min(2).max(50).required() }),
    type: "query",
  })
  ping() {}
}
export class SanitizeMiddleware {}
```

### @Status, @Ok200, @Ok201, @Ok204

These decorators set the HTTP status code for responses. `@Status` accepts a numeric status code, while `@Ok200`, `@Ok201`, and `@Ok204` are shortcuts for common status codes 200, 201, and 204 respectively.

```typescript
import { Status, Ok200, Ok201, Ok204 } from "@heliosjs/middlewares";
import { GET, Controller } from "@heliosjs/core";

@Controller("api")
export class ApiController {
  @Status(201)
  @GET("ping")
  ping() {}
}
```

### @Use

The `@Use` decorator applies one or more middleware functions or classes to a controller or route. It accepts a single middleware or an array of middlewares.

```typescript
import { Use } from "@heliosjs/middlewares";
import { AuthMiddleware } from "./auth.middleware";

@Use(AuthMiddleware)
export class ProtectedController {}

@Use([AuthMiddleware, LoggerMiddleware])
export class AdminController {}
```

## Summary

These middleware annotations provide a powerful and declarative way to manage middleware in HeliosJS applications, enabling clean and maintainable code for handling cross-cutting concerns such as error handling, CORS, sanitization, response status, and middleware application.

For more details, refer to the `middlewares` package source code and HeliosJS documentation.
