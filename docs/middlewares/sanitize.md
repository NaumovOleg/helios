# Sanitize Middleware Decorator

The `@Sanitize` decorator applies sanitization configurations to a controller or method.

## Purpose

This decorator defines how incoming request data should be sanitized before processing. It can be applied at the class level to apply sanitization globally or at the method level for fine-grained control.

## Usage

Apply the `@Sanitize` decorator to a class or method, providing one or more sanitization configuration objects.

## Parameters

- `sanitizeConfig: SanitizerConfig | SanitizerConfig[]` - A single or array of sanitization configuration objects.

## Example

```typescript
// Apply sanitization to all methods in a controller
@Sanitize({ trim: true, escape: true })
class MyController {
  // ...
}

// Apply multiple sanitization rules to a specific method
@Sanitize([
  { trim: true },
  { escape: true }
])
async myMethod() {
  // ...
}
```

## Metadata Handling

The decorator attaches the sanitization configuration(s) as metadata on the target class or method using a specific metadata key (`SANITIZE`). This metadata is accessible via the Reflect API and is used internally by the framework to apply sanitization logic during request handling.

## Remarks

- Multiple sanitization configurations can be applied by passing an array.
- This decorator helps ensure data cleanliness and security by applying consistent sanitization rules.
