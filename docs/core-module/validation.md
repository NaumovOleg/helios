---
sidebar_position: 5
---

# Validation with DTOs and class-validator

You can add validation to your request bodies using DTOs (Data Transfer Objects) and decorators from the `class-validator` package. This helps ensure that incoming data meets your requirements before your controller logic runs.

Here's an example of how to define DTOs and use them in a controller:

```typescript
import { Controller, Post, Body, HttpError } from "@heliosjs/core";
import { IsString, IsBoolean, IsOptional, MinLength, ValidationOptions } from "class-validator";

// DTO for creating a task
class CreateTaskDto {
  @IsString()
  @MinLength(3)
  title!: string;
}

// DTO for updating a task
class UpdateTaskDto {
  @IsOptional()
  @IsString()
  @MinLength(3)
  title?: string;

  @IsOptional()
  @IsBoolean()
  completed?: boolean;
}

interface Task {
  id: number;
  title: string;
  completed: boolean;
}

let tasks: Task[] = [];
let nextId = 1;


const options: ValidationOptions

@Controller("/tasks")
export class TasksController {
  @Post("/")
  createTask(@Body(CreateTaskDto, options) body: CreateTaskDto): Task {
    const newTask: Task = {
      id: nextId++,
      title: body.title.trim(),
      completed: false,
    };

    tasks.push(newTask);
    return newTask;
  }

  @Post("/:id")
  updateTask(
    @Body(UpdateTaskDto, options, 'property') body: UpdateTaskDto,
  ): Task | { error: string } {
    // Example update logic here
    // This is just a placeholder
    return { error: "Not implemented" };
  }
}
```

This example shows how to use DTOs with validation decorators to enforce rules on incoming data.