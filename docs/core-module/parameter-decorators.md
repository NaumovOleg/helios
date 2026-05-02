---
sidebar_position: 6
---

# Routing & Parameters

HeliosJS provides powerful routing capabilities with intuitive decorators. Let's explore all the ways you can define routes and extract data from requests.

## Route Decorators

HeliosJS supports all standard HTTP methods:

| Decorator        | Method  | Description              |
| ---------------- | ------- | ------------------------ |
| `@Get(path)`     | Get     | Retrieve data            |
| `@Post(path)`    | Post    | Create new resources     |
| `@Put(path)`     | Put     | Update entire resources  |
| `@Patch(path)`   | Patch   | Update partial resources |
| `@Delete(path)`  | Delete  | Remove resources         |
| `@Options(path)` | Options | Get allowed methods      |
| `@Head(path)`    | Head    | Get headers only         |

## Basic Routes

```typescript
import { Controller, Get, Post, Put, Patch, Delete } from "@heliosjs/core";

@Controller("/users")
export class UserController {
  @Get("/")
  findAll() {
    return [{ id: 1, name: "Alice" }];
  }

  @Post("/")
  create(@Body() data: any) {
    return { id: 2, ...data };
  }

  @Put("/:id")
  update(@Param("id") id: string, @Body() data: any) {
    return { id: parseInt(id), ...data };
  }

  @Patch("/:id")
  partialUpdate(@Param("id") id: string, @Body() data: any) {
    return { id: parseInt(id), ...data };
  }

  @Delete("/:id")
  remove(@Param("id") id: string) {
    return { deleted: true, id: parseInt(id) };
  }
}
```

## Route Parameters

### URL Parameters (@Param)

Extract dynamic segments from the URL:

```typescript
import { Controller, Get, Param } from "@heliosjs/core";

@Controller("/users")
export class UserController {
  // Match: /users/123
  @Get("/:id")
  getUserById(@Param("id") id: string) {
    return { userId: parseInt(id) };
  }

  // Match: /users/123/posts/456
  @Get("/:userId/posts/:postId")
  getUserPost(
    @Param("userId") userId: string,
    @Param("postId") postId: string,
  ) {
    return {
      userId: parseInt(userId),
      postId: parseInt(postId),
    };
  }
}
```

### Query Parameters (@Query)

Extract query string parameters:

```typescript
import { Controller, Get, Query } from "@heliosjs/core";

@Controller("/users")
export class UserController {
  // Match: /users?page=1&limit=10&search=john
  @Get("/")
  findAll(
    @Query("page") page?: string,
    @Query("limit") limit?: string,
    @Query("search") search?: string,
  ) {
    return {
      page: page ? parseInt(page) : 1,
      limit: limit ? parseInt(limit) : 10,
      search: search || "",
    };
  }

  // Get all query parameters as an object
  @Get("/filter")
  filter(@Query() query: any) {
    return { filters: query };
  }
}
```

### Request Body (@Body)

Extract the request body:

```typescript
import { Controller, Post, Body } from "@heliosjs/core";

interface CreateUserDto {
  name: string;
  email: string;
  age?: number;
}

@Controller("/users")
export class UserController {
  @Post("/")
  create(@Body() userData: CreateUserDto) {
    // userData contains the parsed JSON body
    return { id: 1, ...userData };
  }

  // Extract specific fields
  @Post("/validate")
  validate(@Body("email") email: string) {
    return { email };
  }
}
```


## Request Headers (@Headers)

Extract HTTP headers:

```typescript
import { Controller, Get, Headers } from "@heliosjs/core";

@Controller("/auth")
export class AuthController {
  @Get("/profile")
  getProfile(
    @Headers("authorization") auth: string,
    @Headers("user-agent") userAgent: string,
  ) {
    return {
      token: auth?.replace("Bearer ", ""),
      userAgent,
    };
  }

  // Get all headers
  @Get("/headers")
  getAllHeaders(@Headers() headers: any) {
    return { headers };
  }
}
```

### Request Object (@Req)

Access the raw request object:

```typescript
import { Controller, Get, Req, Request } from "@heliosjs/core";
import { IncomingMessage } from "http";

@Controller("/request")
export class RequestController {
  @Get("/info")
  getRequestInfo(@Req() req: Request) {
    return {
      method: req.method,
      url: req.url,
      headers: req.headers,
      httpVersion: req.httpVersion,
    };
  }
}
```

### Response Object (@Res)

Access the raw response object for manual control:

```typescript
import { Controller, Get, Res, Response } from "@heliosjs/core";

@Controller("/response")
export class ResponseController {
  @Get("/custom")
  sendCustomResponse(@Res() res: Response) {
    res.statusCode = 201;
    res.setHeader("X-Custom-Header", "Hello");
  }
}
```

## Combined Example

Here's a complete controller showing all parameter types:

```typescript
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Param,
  Query,
  Body,
  Headers,
  Req,
  Res,
} from "@heliosjs/core";

interface Product {
  id: number;
  name: string;
  price: number;
}

@Controller("/products")
export class ProductController {
  private products: Product[] = [];
  private nextId = 1;

  // Get /products?category=electronics&minPrice=10
  @Get("/")
  findAll(
    @Query("category") category?: string,
    @Query("minPrice") minPrice?: string,
  ) {
    let filtered = [...this.products];

    if (category) {
      filtered = filtered.filter((p) => p.name.includes(category));
    }

    if (minPrice) {
      const price = parseInt(minPrice);
      filtered = filtered.filter((p) => p.price >= price);
    }

    return filtered;
  }

  // Get /products/123
  @Get("/:id")
  findOne(@Param("id") id: string) {
    const product = this.products.find((p) => p.id === parseInt(id));
    if (!product) {
      throw new Error("Product not found");
    }
    return product;
  }

  // Post /products
  @Post("/")
  create(
    @Body() productData: Omit<Product, "id">,
    @Headers("authorization") auth: string,
  ) {
    // Check authentication
    if (!auth) {
      throw new Error("Unauthorized");
    }

    const newProduct: Product = {
      id: this.nextId++,
      ...productData,
    };

    this.products.push(newProduct);
    return newProduct;
  }

  // Put /products/123
  @Put("/:id")
  update(@Param("id") id: string, @Body() productData: Partial<Product>) {
    const productId = parseInt(id);
    const index = this.products.findIndex((p) => p.id === productId);

    if (index === -1) {
      throw new Error("Product not found");
    }

    this.products[index] = { ...this.products[index], ...productData };
    return this.products[index];
  }

  // Delete /products/123
  @Delete("/:id")
  remove(@Param("id") id: string, @Req() req: any, @Res() res: any) {
    const productId = parseInt(id);
    const index = this.products.findIndex((p) => p.id === productId);

    if (index === -1) {
      res.statusCode = 404;
      return { error: "Product not found" };
    }

    this.products.splice(index, 1);
    return { deleted: true, id: productId };
  }
}
```

## Parameter Summary

| Decorator         | Source         | Usage                         |
| ----------------- | -------------- | ----------------------------- |
| `@Param(name?)`   | URL path       | /users/:id 12; `@Param('id')` |
| `@Query(name?)`   | Query string   | ?page=1 12; `@Query('page')`  |
| `@Body(name?)`    | Request body   | JSON payload                  |
| `@Headers(name?)` | HTTP headers   | Authorization header          |
| `@Files(name?)`   | Multipart data |                               |
| `@Req()`          | Raw request    | Full Incoming Message object  |
| `@Res()`          | Raw response   | Full Server Response object   |

## Multipart Form Data (@Files)

Extract multipart form data from requests, optionally by field name:

```typescript
import { Controller, Post, Files } from "@heliosjs/core";

@Controller("/upload")
export class UploadController {
  @Post("/")
  uploadFile(@Files("file") file: any) {
    // file contains the uploaded file data
    return { uploaded: true, file };
  }
}
```

| Decorator       | Source              | Usage                                                 |
| --------------- | ------------------- | ----------------------------------------------------- |
| `@Files(name?)` | Multipart form data | Extract multipart field by name or all multipart data |

This corresponds to the @Multipart parameter decorator used in the codebase as @Files.

## Route Priority

Routes are matched in the order they are defined. More specific routes should come before general ones:

```typescript
@Controller("/users")
export class UserController {
  // Specific route first
  @Get("/profile")
  getProfile() {
    return { page: "profile" };
  }

  // General route last
  @Get("/:id")
  getUserById(@Param("id") id: string) {
    return { userId: id };
  }
}
```
