# Cors Middleware Decorator

The `@Cors` decorator configures Cross-Origin Resource Sharing (CORS) settings for HTTP controllers or methods.

## Purpose

This decorator enables you to specify CORS policies such as allowed origins, HTTP methods, and the status code to return for successful OPTIONS requests. It can be applied at the class level to affect all endpoints within a controller or at the method level to customize CORS for specific endpoints.

## Usage

Apply the `@Cors` decorator to a class or method, optionally providing a configuration object to customize CORS behavior.

## Parameters

- `config?: CORSConfig` - Configuration object for CORS settings.
  - `origin?: string | string[]` - Specifies the allowed origin(s) for CORS requests. Defaults to '\*'.
  - `optionsSuccessStatus?: number` - HTTP status code to return for successful OPTIONS requests. Defaults to 204.
  - `methods?: string[]` - Array of allowed HTTP methods for CORS. Defaults to all HTTP methods.

## Example

```typescript
// Apply CORS with default settings to all endpoints in a controller
@Cors()
class MyController {
  // ...
}

// Apply CORS with custom settings to a specific method
@Cors({ origin: 'https://example.com', methods: ['GET', 'POST'] })
async myMethod() {
  // ...
}
```

## Metadata Handling

The decorator attaches the CORS configuration as metadata on the target class or method using a specific metadata key (`CORS_METADATA`). This metadata is accessible via the Reflect API and is used internally by the framework to enforce CORS policies during request handling.

## Remarks

- The default origin is '\*', allowing all origins.
- The default allowed methods include all HTTP methods.
- The `optionsSuccessStatus` is used to specify the status code for successful OPTIONS preflight requests.
- Applying this decorator helps in managing cross-origin requests securely and flexibly.
