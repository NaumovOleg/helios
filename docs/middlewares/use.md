# Use Middleware Decorator

The `@Use` decorator registers middleware(s) at the controller or method level.

## Purpose

Middlewares are functions executed before the route handler. They can be used to modify request/response, perform logging, handle authentication/authorization, or short-circuit request handling.

## Usage

Apply the `@Use` decorator to a class or method, providing a single middleware function or an array of middleware functions.

## Parameters

- `middleware: MiddlewareCB | MiddlewareCB[]` - A single middleware function or an array of middleware functions.

## Examples

### Single Middleware

```typescript
@Use((req, res, next) => {
  console.log("Request received");
  next();
})
class MyController {}
```

### Multiple Middlewares

```typescript
@Use([authMiddleware, loggingMiddleware])
class MyController {}
```

### Method-level Middleware

```typescript
class MyController {
  @Use(authMiddleware)
  getData() {}
}
```

## Metadata Handling

The decorator attaches the middleware(s) as metadata on the target class or method using a specific metadata key (`MIDDLEWARES_CONFIG`). This metadata is accessible via the Reflect API and is used internally by the framework during request processing to execute middlewares in the defined order.

## Remarks

- Middlewares are executed in the order they are defined.
- Can be applied at both class and method levels.
- Useful for cross-cutting concerns like logging, authentication, and request modification.
