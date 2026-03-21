---
sidebar_position: 3
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
  Get,
  Post,
  Put,
  Delete,
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
  @Get("/")
  getAllTasks(): Task[] {
    return tasks;
  }

  @Get("/:id")
  getTaskById(@Param("id") id: string): Task | { error: string } {
    const taskId = parseInt(id);
    const task = tasks.find((t) => t.id === taskId);

    if (!task) {
      return { error: "Task not found" };
    }

    return task;
  }

  @Post("/")
  createTask(@Body() body: { title: string }): Task {
    const newTask: Task = {
      id: nextId++,
      title: body.title,
      completed: false,
    };

    tasks.push(newTask);
    return newTask;
  }

  @Put("/:id")
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

  @Delete("/:id")
  deleteTask(@Param("id") id: string): { message: string } | { error: string } {
    const taskId = parseInt(id);
    const index = tasks.findIndex((t) => t.id === taskId);

    if (index === -1) {
      return { error: "Task not found" };
    }

    tasks.splice(index, 1);
    return { message: "Task deleted successfully" };
  }
}
```

## Step 2: Understanding the Decorators

| Decorator               | Purpose                                                 |
| ----------------------- | ------------------------------------------------------- |
| `@Controller('/tasks')` | Defines the base route for all methods in this class    |
| `@Get('/')`             | Handles GET requests to `/tasks`                        |
| `@Get('/:id')`          | Handles GET requests to `/tasks/1` (with URL parameter) |
| `@Post('/')`            | Handles POST requests to `/tasks`                       |
| `@Put('/:id')`          | Handles PUT requests to `/tasks/1`                      |
| `@Delete('/:id')`       | Handles DELETE requests to `/tasks/1`                   |
| `@Param('id')`          | Extracts the `id` parameter from the URL                |
| `@Body()`               | Extracts the request body                               |

## Step 3: Register the Controller

Update `src/app.ts` to include your new controller:

```typescript
import "reflect-metadata";
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

## Step 5: Add Validation

Let's add some basic validation to our controller:

```typescript
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Body,
  Param,
  HttpError,
} from "@heliosjs/core";

@Controller("/tasks")
export class TasksController {
  // ... existing code ...

  @Post("/")
  createTask(@Body() body: { title: string }): Task {
    // Validation
    if (!body.title || body.title.trim() === "") {
      throw new HttpError(400, "Title is required");
    }

    if (body.title.length < 3) {
      throw new HttpError(400, "Title must be at least 3 characters");
    }

    const newTask: Task = {
      id: nextId++,
      title: body.title.trim(),
      completed: false,
    };

    tasks.push(newTask);
    return newTask;
  }

  @Put("/:id")
  updateTask(
    @Param("id") id: string,
    @Body() body: Partial<Task>,
  ): Task | { error: string } {
    const taskId = parseInt(id);
    const task = tasks.find((t) => t.id === taskId);

    if (!task) {
      throw new HttpError(404, "Task not found");
    }

    // Validation
    if (body.title !== undefined && body.title.trim() === "") {
      throw new HttpError(400, "Title cannot be empty");
    }

    if (body.title !== undefined && body.title.length < 3) {
      throw new HttpError(400, "Title must be at least 3 characters");
    }

    if (body.title !== undefined) task.title = body.title.trim();
    if (body.completed !== undefined) task.completed = body.completed;

    return task;
  }
}
```

## Step 6: Test Validation

Try creating a task without a title:

```bash
curl -X POST http://localhost:3000/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": ""}'
```

Expected response:

```json
{
  "statusCode": 400,
  "message": "Title is required"
}
```

## What You've Learned

- ✅ Creating controllers with `@Controller`
- ✅ Defining routes with `@Get`, `@Post`, `@Put`, `@Delete`
- ✅ Using `@Param` to extract URL parameters
- ✅ Using `@Body` to extract request body
- ✅ Adding validation with `HttpError`
- ✅ Building a complete CRUD API

## Next Steps

Now that you know how to create controllers and routes, let's dive deeper into routing and parameters.

[Next: Routing & Parameters](./routing-parameters.md)
