# Pipe Middleware Decorator

The `@Pipe` decorator registers a data transformation pipe.

## Purpose

Pipes are used to transform incoming request data before it reaches the route handler. They can be applied at the controller level and modify parts of the request such as body, query, params, or headers.

## Usage

Apply the `@Pipe` decorator to a class or method, providing a configuration object containing transformation functions for different parts of the request.

## Parameters

- `config: Pipe` - Configuration object with transformation functions for different parts of the request.

## Example

```typescript
@Pipe({
  body: (body) => ({ ...body, name: body.name.trim() }),
  query: (query) => ({ ...query, page: Number(query.page) }),
})
class MyController {}
```

## Metadata Handling

The decorator attaches the pipe configuration as metadata on the target class or method using a specific metadata key (`CONTROLLER_CONFIGUARTION`). This metadata is accessible via the Reflect API and is used internally by the framework during request processing to apply the transformations.

## Remarks

- Each function in the pipe receives raw data and must return the transformed value.
- Pipes are executed before controller methods.
- Common use cases include data normalization, type casting, and sanitization.
