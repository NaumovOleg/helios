# Catch Middleware Decorator

The `@Catch` decorator registers a global or controller-level error handler.

## Purpose

The `@Catch` decorator is designed to handle errors that occur within the scope of the decorated target, such as a controller or service. It allows you to define a custom error handling callback that will be invoked whenever an error is thrown, enabling centralized and consistent error management.

## Usage

Apply the `@Catch` decorator to a class or method and provide an error handler function. This function receives the error and the context in which the error occurred.

## Parameters

- `handler: ErrorHandler` - A callback function that handles errors. It typically accepts two parameters: the error object and the context (e.g., request, response, or other relevant data).

## Example

```typescript
@Catch(async (error, context) => {
  console.error("Error caught:", error);
  // Implement custom error handling logic here
})
class MyController {
  // Controller methods
}
```

## Metadata Handling

The decorator attaches the error handler as metadata on the target class or method using a specific metadata key (`CATCH`). This metadata is accessible via the Reflect API and is used internally by the framework to invoke the registered error handler during runtime when errors occur.

## Remarks

- The error handler can be asynchronous.
- Multiple error handlers can be registered by applying the decorator multiple times.
- This decorator helps in separating error handling logic from business logic, improving code maintainability and readability.
