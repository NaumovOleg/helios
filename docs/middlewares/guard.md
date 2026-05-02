# Guard Middleware Decorator

The `@Guard` decorator registers a guard to control access to controllers or methods.

## Purpose

Guards are used to validate incoming requests before they reach route handlers. They act as gatekeepers that determine whether a request should be allowed to proceed. If any guard returns `false`, the request is blocked and further processing is stopped.

## Usage

Apply the `@Guard` decorator to a class or method, passing a guard class or function that implements the guard logic.

## Parameters

- `guard: GuardInstance | GuardFunction` - A guard class or function used to determine whether the request is allowed to proceed.

## Examples

### Function

```typescript
@Guard((req, res) => {
  // Allow only if authorization header is present
  return !!req.headers.authorization;
})
class MyController {}
```

### Class-based GuardClass



```typescript
import {GuardInstance} from '@heliosjs/core'
class AuthGuard implements GuardInstance {
  canActivate(req: Request, res: Response) {
    // Allow only if authorization header is present
    // return !!req.headers.authorization;
     return "Forbidden";
  }
}

@Guard(AuthGuard)
class MyController {}
```

## Metadata Handling

The decorator attaches the guard(s) as metadata on the target class or method using a specific metadata key (`CONTROLLER_CONFIGURATION`). This metadata is accessible via the Reflect API and is used internally by the framework during request processing to enforce guard checks.

## Remarks

- Guards are executed before controller methods.
- They can be used for authentication, authorization, and request validation.
- If any guard returns `false`, the request handling is stopped.
- Multiple guards can be applied by using the decorator multiple times or passing an array (if supported).
