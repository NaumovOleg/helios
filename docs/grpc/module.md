# gRPC Module Documentation

## Introduction

The gRPC module provides a powerful and flexible way to build gRPC servers and clients within your application. It leverages the gRPC protocol to enable efficient, low-latency communication between services using protocol buffers.

This module is designed to integrate seamlessly with your application, providing decorators and utilities to define gRPC services, methods, and clients with ease.

## Overview

### GrpcModule

The `GrpcModule` is the core module that you import into your application to enable gRPC functionality. It provides configuration options to set up gRPC servers and clients.

### GrpcServer

`GrpcServer` is the server implementation that listens for incoming gRPC requests and dispatches them to the appropriate service methods.

### GrpcClient

`GrpcClient` is the client implementation used to connect to remote gRPC servers and invoke their methods.

### Decorators

- `@GrpcService`: Decorates a class to define it as a gRPC service.
- `@GrpcMethod`: Decorates a method within a service class to define it as a gRPC method.
- `@InjectGrpcClient`: Decorator to inject a configured gRPC client instance into your service or controller.

## Basic Usage Example

Below is a basic example demonstrating how to set up a gRPC server and client.

### Server Setup (Referencing `examples/src/grpc/server.ts`)

```typescript
import { GrpcModule, GrpcService, GrpcMethod } from '@yourorg/grpc';

@GrpcService()
class HeroService {
  @GrpcMethod('FindOne')
  findOne(data: { id: number }) {
    return { id: data.id, name: 'Hero ' + data.id };
  }
}

const grpcModule = GrpcModule.forRoot({
  server: {
    port: 50051,
  },
  clients: [],
});

// Application bootstrap code to start the server
```

### Client Setup (Referencing `examples/src/grpc/client.ts`)

```typescript
import { GrpcModule, InjectGrpcClient, GrpcClient } from '@yourorg/grpc';

class HeroClientService {
  
  constructor(@InjectGrpcClient('hero') private client: GrpcClient)

  async findOne(id: number) {
    return this.client.send('HeroService.FindOne', { id });
  }
}

const grpcModule = GrpcModule.forRoot({
  clients: [
    {
      name: 'hero',
      url: 'localhost:50051',
    },
  ],
});

// Application bootstrap code to start the client
```

## Module Configuration

The `GrpcModule` can be configured using the `forRoot` method, which accepts an options object.

### Options

- `server`: Configuration for the gRPC server.
  - `port`: The port number on which the server listens.
  - Additional server options can be provided as needed.

- `clients`: An array of client configurations.
  - Each client configuration includes:
    - `name`: A unique identifier for the client.
    - `url`: The address of the gRPC server to connect to.
    - Additional client options can be provided.

Example:

```typescript
GrpcModule.forRoot({
  server: {
    port: 50051,
  },
  clients: [
    {
      name: 'hero',
      url: 'localhost:50051',
    },
  ],
});
```

This setup allows you to run a gRPC server and connect clients to it within the same application or across different services.

---

For more detailed examples and advanced usage, please refer to the example files in the `examples/src/grpc` folder.