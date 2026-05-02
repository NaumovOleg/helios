---
sidebar_position: 4
---

# Built-in Error Handling

Error Handling

Learn how to validate incoming data and handle errors gracefully in your HeliosJS application.



HeliosJS provides a built-in error handling system that catches exceptions and returns appropriate HTTP responses.

## Custom Error Classes

HeliosJS provides a set of custom error classes for common error scenarios. These are located in `packages/src/utils/core/error/`:

- `UnauthorizedError`
- `DependencyFailedError`
- `DuplicateEntryError`
- `ForbiddenError`
- `InvalidStateError`
- `NotFoundError`
- `ServiceUnavailableError`
- `ValidationError`
- `InternalServerError`
- `RateLimitExceededError`

## Using Custom Errors

```typescript
import { Controller, Get, Post, Body, Param, ValidationError, NotFoundError } from "@heliosjs/core";


interface User {
  id: number;
  name: string;
  email: string;
}

@Controller("/users")
export class UserController {
  private users: User[] = [];
  private nextId = 1;

  @Get("/:id")
  getUser(@Param("id") id: string) {
    const userId = parseInt(id);
    const user = this.users.find((u) => u.id === userId);

    if (!user) {
      throw new NotFound("User", userId);
    }

    return user;
  }

  @Post("/")
  createUser(@Body() userData: Partial<User>) {
    // Custom validation
    if (!userData.name) {
      throw new ValidationError("name", "Name is required");
    }
    ...
  }
}
```

## Global Error Handling with @Catch Decorator

HeliosJS provides a `@Catch` decorator from `packages/src/middlewares/src/catch.ts` to define global error handlers.

The `@Catch` decorator accepts an error handling function and applies it globally to catch and process errors.

Example:

```typescript
import { Catch } from "@heliosjs/middlewares";
import { Request, Response } from "@heliosjs/middlewares";

const errorHandler = (error: Error, req: Request, res: Response) => {
  const errorData = {
    requestId:req.requestId,
    message: error.message,
    timestamp: new Date().toISOString(),
  }

  if (error.name === "ValidationError") {
    res.status = 400
    errorData.field = error.field,
  }

  if (error.name === "NotFound") {
    res.status = 404
  }

  if (error.name === "Forbidden") {
    res.status = 403
  }

  return res.error(errorData)

};
@Controller({ prefix: 'user' })
@Catch(errorHandler)
export class UserController {}
```

## Register Global Error Handler

```typescript
// src/app.ts
import { HeliosHttp } from "@heliosjs/http";
import { GlobalErrorHandler } from "./errors/global.handler";
import { UserController } from "./controllers/user.controller";

@Server({
  errorHandler: errorHandler
  controllers: [Root, Socket, MetricsController],
})
export class App {}
```
