---
sidebar_position: 3
---

# Controllers

Now that your project is set up, let's create your first real controller. We'll build a simple task management API.

## What is a Controller?

A controller is a class that handles incoming HTTP requests. Each method in a controller corresponds to a route endpoint.

## Step 1: Create a Tasks Controller

Create `src/controllers/user.controller.ts`:

```typescript
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  Patch,
  NotFoundError,
} from "@heliosjs/core";

// Define a type for our task
interface User {
  id: number;
  name: string;
}

// In-memory storage (replace with database later)
let tasks: User[] = [];
let nextId = 1;

@Controller("/user")
export class UserController {
  @Get("/")
  getList(): User[] {
    return users;
  }

  @Get("/:id")
  getById(@Param("id") id: string) {
    const userId = parseInt(id);
    const user = users.find((t) => t.id === userId);

    if (!user) {
      return new NotFoundError("User not found");
    }

    return user;
  }

  @Post("/")
  create(@Body() user: User) {
    users.push(user);
    return user;
  }

  @Put("/")
  createOrUpdate(@Body() body: User) {
    const userId = parseInt(id);
    const user = users.find((t) => t.id === userId);

    if (!user) {
      users.push(body);
    } else {
      Object.assign(user, body);
    }
    return body;
  }

  @Patch("/:id")
  update(@Param("id") id: string, @Body() body: Partial<User>) {
    const userId = parseInt(id);
    const user = users.find((t) => t.id === userId);

    if (!user) {
      return new NotFoundError("User not found");
    }
    Object.assign(user, body);

    return user;
  }

  @Delete("/:id")
  delete(@Param("id") id: string) {
    const userId = parseInt(id);
    const user = users.find((t) => t.id === userId);

    if (!user) {
      return new NotFoundError("User not found");
    }

    tasks.splice(index, 1);
    return userId;
  }

  @Any()
  async any(@Req() resp: any) {}
}
```

## Step 2: Understanding the Decorators

| Decorator             | Purpose                                                |
| --------------------- | ------------------------------------------------------ |
| `@Controller('user')` | Defines the base route for all methods in this class   |
| `@Get('/')`           | Handles Get requests to `/user`                        |
| `@Get('/:id')`        | Handles Get requests to `/user/1` (with URL parameter) |
| `@Post('/')`          | Handles Post requests to `/user`                       |
| `@Put('/')`           | Handles Put requests to `/user`                        |
| `@Delete('/:id')`     | Handles Delete requests to `/user/1`                   |
| `@Patch('/:id')`      | Handles Patch requests to `/user/1`                    |
| `@Any()`              | Handles all requests                                   |
| `@Param('id')`        | Extracts the `id` parameter from the URL               |
| `@Body()`             | Extracts the request body                              |

## Step 3: Register the Controller

Update `src/app.ts` to include your new controller:

```typescript
import { Server } from "@heliosjs/http";
import { UserController } from "./controllers/user.controller";

@Server({ controllers: [UserController] })
export class App {}
```

Or You can use nested routing:

```typescript
@Controller("/api", controllers:[UserController])
```

## Step 4: Test Your API

Start your server:

```bash
npm run dev
```

Now test the endpoints using curl or any API client.

### Create a User (Post)

```bash
curl -X Post http://localhost:3000/user \
  -H "Content-Type: application/json" \
  -d '{"name": "John Dow", "id": 1}'
```

Expected response:

```json
{ "id": 1, "name": "John Dow" }
```

### Get All Users (Get)

```bash
curl http://localhost:3000/user
```

Expected response:

```json
[{ "id": 1, "name": "John Dow" }]
```

### Get a Single User (Get)

```bash
curl http://localhost:3000/user/1
```

Expected response:

```json
{ "id": 1, "name": "John Dow" }
```

### Delete User (Delete)

```bash
curl -X Delete http://localhost:3000/user/1
```

Expected response:

```json
{
  "message": "User deleted successfully"
}
```

## What You've Learned

- [32m[1m[22m[39m Creating controllers with `@Controller`
- [32m[1m[22m[39m Defining routes with `@Get`, `@Post`, `@Put`, `@Delete`
- [32m[1m[22m[39m Using `@Param` to extract URL parameters
- [32m[1m[22m[39m Using `@Body` to extract request body
- [32m[1m[22m[39m Building a complete CRUD API
