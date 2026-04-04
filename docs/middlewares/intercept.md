# Intercept Middleware Decorator

The `@Intercept` decorator registers an interceptor at the controller or method level.

## Purpose

Interceptors are functions that wrap the execution of a route handler. They allow you to run logic before and after the handler is executed, enabling modification of responses or handling of cross-cutting concerns such as logging, caching, or error handling.

## Usage

Apply the `@Intercept` decorator to a class or method, providing an interceptor callback function.

## Parameters

- `handler: InterceptorCB` - The interceptor callback function which receives the context and a `next` function to invoke the next handler in the chain.

## Example

```typescript
// Logging interceptor applied at the controller level
@Intercept(async (ctx, next) => {
  console.log("Before handler execution");
  const result = await next();
  console.log("After handler execution");
  return result;
})
class MyController {}

// Method-level interceptor
class MyController {
  @Intercept(async (ctx, next) => {
    // Custom logic before method
    return next();
  })
  getData() {}
}
```

## Metadata Handling

The decorator attaches the interceptor(s) as metadata on the target class or method using a specific metadata key (`MIDDLEWARES_CONFIG`). This metadata is accessible via the Reflect API and is used internally by the framework during request processing to execute interceptors in the defined order.

## Remarks

- Interceptors can modify the result returned by the handler.
- They are executed in the order they are applied.
- Common use cases include logging, response transformation, caching, and error handling.
