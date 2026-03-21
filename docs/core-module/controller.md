---
sidebar_position: 2
---

# Controllers

Now that your project is set up, let's create your first real controller. We'll build a simple task management API.

## What is a Controller?

A controller is a class that handles incoming HTTP requests. Each method in a controller corresponds to a route endpoint.

## Step 1: Create a Tasks Controller

Create `src/controllers/tasks.controller.ts`:

```typescript
import {
  Controller,
  GET,
  POST,
  PUT,
  DELETE,
  Body,
  Param,
} from "@heliosjs/core";

// Define a type for our task
interface Task {
  id: number;
  title: string;
  completed: boolean;
}

// In-memory storage (replace with database later)
let tasks: Task[] = [];
let nextId = 1;

@Controller("/tasks")
export class TasksController {
  @GET("/")
  getAllTasks(): Task[] {
    return tasks;
  }

  @GET("/:id")
  getTaskById(@Param("id") id: string): Task | { error: string } {
    const taskId = parseInt(id);
    const task = tasks.find((t) => t.id === taskId);

    if (!task) {
      return { error: "Task not found" };
    }

    return task;
  }

  @POST("/")
  createTask(@Body() body: { title: string }): Task {
    const newTask: Task = {
      id: nextId++,
      title: body.title,
      completed: false,
    };

    tasks.push(newTask);
    return newTask;
  }

  @PUT("/:id")
  updateTask(
    @Param("id") id: string,
    @Body() body: Partial<Task>,
  ): Task | { error: string } {
    const taskId = parseInt(id);
    const task = tasks.find((t) => t.id === taskId);

    if (!task) {
      return { error: "Task not found" };
    }

    if (body.title !== undefined) task.title = body.title;
    if (body.completed !== undefined) task.completed = body.completed;

    return task;
  }

  @DELETE("/:id")
  deleteTask(@Param("id") id: string): { message: string } | { error: string } {
    const taskId = parseInt(id);
    const index = tasks.findIndex((t) => t.id === taskId);

    if (index === -1) {
      return { error: "Task not found" };
    }

    tasks.splice(index, 1);
    return { message: "Task deleted successfully" };
  }

  @ANY()
  async any(@Req() resp: any) {}
}
```

## Step 2: Understanding the Decorators

| Decorator               | Purpose                                                 |
| ----------------------- | ------------------------------------------------------- |
| `@Controller('/tasks')` | Defines the base route for all methods in this class    |
| `@GET('/')`             | Handles GET requests to `/tasks`                        |
| `@GET('/:id')`          | Handles GET requests to `/tasks/1` (with URL parameter) |
| `@POST('/')`            | Handles POST requests to `/tasks`                       |
| `@PUT('/:id')`          | Handles PUT requests to `/tasks/1`                      |
| `@DELETE('/:id')`       | Handles DELETE requests to `/tasks/1`                   |
| `@PATCH('/:id')`        | Handles PATCH requests to `/tasks/1`                    |
| `@ANY()`                | Handles all requests                                    |
| `@Param('id')`          | Extracts the `id` parameter from the URL                |
| `@Body()`               | Extracts the request body                               |

## Step 3: Register the Controller

Update `src/app.ts` to include your new controller:

```typescript
import { Server } from "@heliosjs/http";
import { HealthController } from "./controllers/health.controller";
import { TasksController } from "./controllers/tasks.controller";

@Server({ controllers: [HealthController, TasksController] })
export class App {}
```

Or You can use nested routing:

```typescript
@Controller("/tasks", controllers:[HealthController])
```

## Interceptors in Controllers

Interceptors are functions defined in the controller decorator that can modify or handle data before the response is sent.

They receive three parameters:

- `data`: The data to be processed or modified.
- `req`: The incoming request object.
- `res`: The outgoing response object.

### Example

```typescript
@Controller("/example", {
  interceptors: [
    (data, req, res) => {
      // Modify data before it is sent
      if (typeof data === "string") {
        return data.toUpperCase();
      }
      return data;
    },
  ],
})
export class ExampleController {
  @GET("/")
  getData(): string {
    return "hello world";
  }
}
```

## Step 4: Test Your API

Start your server:

```bash
npm run dev
```

Now test the endpoints using curl or any API client.

### Create a Task (POST)

```bash
curl -X POST http://localhost:3000/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn HeliosJS"}'
```

Expected response:

```json
{
  "id": 1,
  "title": "Learn HeliosJS",
  "completed": false
}
```

### Get All Tasks (GET)

```bash
curl http://localhost:3000/tasks
```

Expected response:

```json
[
  {
    "id": 1,
    "title": "Learn HeliosJS",
    "completed": false
  }
]
```

### Get a Single Task (GET)

```bash
curl http://localhost:3000/tasks/1
```

Expected response:

```json
{
  "id": 1,
  "title": "Learn HeliosJS",
  "completed": false
}
```

### Update a Task (PUT)

```bash
curl -X PUT http://localhost:3000/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"completed": true}'
```

Expected response:

```json
{
  "id": 1,
  "title": "Learn HeliosJS",
  "completed": true
}
```

### Delete a Task (DELETE)

```bash
curl -X DELETE http://localhost:3000/tasks/1
```

Expected response:

```json
{
  "message": "Task deleted successfully"
}
```

## What You've Learned

- [32m[1m[22m[39m Creating controllers with `@Controller`
- [32m[1m[22m[39m Defining routes with `@GET`, `@POST`, `@PUT`, `@DELETE`
- [32m[1m[22m[39m Using `@Param` to extract URL parameters
- [32m[1m[22m[39m Using `@Body` to extract request body
- [32m[1m[22m[39m Building a complete CRUD API

## Next Steps

Now that you know how to create controllers and routes, let's dive deeper into routing and parameters.

[Next: Routing & Parameters](./routing-parameters.md)
