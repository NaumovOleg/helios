# gRPC Module Examples

This document provides comprehensive examples demonstrating the usage of the gRPC module, including server setup, client usage, and module initialization.

## Example Flow

The examples show how to define a gRPC service, set up a gRPC server, create a gRPC client, and perform RPC calls between the client and server.

---

## Server Example (`server.ts`)

```typescript
import { join } from 'node:path';
import { GrpcMethod, GrpcService, InjectGrpcClient } from '@heliosjs/grpc';

@GrpcService('User', {
  protoPath: join(__dirname, './hero.proto'),
  package: 'hero',
})
export class UserService {
  constructor(@InjectGrpcClient('User') private client: any) {
  
  }

  @GrpcMethod('getOne')
  getOne(body:{name:string}) {
    return { message: body.name };
  }
}
```

This example defines a `UserService` gRPC service with a `getOne` method. The service uses the `@GrpcService` decorator to specify the proto file and package, and the `@GrpcMethod` decorator to define the RPC method.

---

## Client Example (`client.ts`)

```typescript
import { join } from "node:path";
import { GrpcClient } from "@heliosjs/grpc";

export const client = new GrpcClient({
  url: "localhost:5001",
  package: "hero",
  protoPath: join(__dirname, "./hero.proto"),
});
```

This example creates a gRPC client configured to connect to the server at `localhost:5001` using the specified proto file and package.

---

## Module Initialization and Server Start (`index.ts`)

```typescript
import { join } from 'node:path';
import { GrpcModule, GrpcServer } from '@heliosjs/grpc';
import { firstValueFrom } from 'rxjs';
import { client } from './client';
import { UserService } from './server';

const grpc = GrpcModule.forRoot({
  server: {
    url: '0.0.0.0:5001',
    package: 'hero',
    protoPath: join(__dirname, './hero.proto'),
  },
  clients: [
    {
      name: 'Book',
      options: {
        url: 'localhost:5002',
        protoPath: join(__dirname, './book.proto'),
        package: 'book',
      },
    },
  ],
});

const server = grpc.getServer();
if (server) {
  server.registerService(UserService);
  server.start();
}

async function callGrpc() {
  const heroService = client.getService<{
    getOne(request: any): any;
  }>('User');
  const response = await firstValueFrom(heroService.getOne({ name: 'John' }));
}

```

This example initializes the gRPC module with server and client configurations, registers the `UserService` on the server, starts the server, and demonstrates making a gRPC call from the client.

---

## How to Run the Examples

1. Ensure you have the `hero.proto` file in the same directory as the examples.
2. Start the gRPC server by running the `index.ts` file (or equivalent build/run command).
3. The client will automatically make a call to the `getOne` method on the server.
4. Observe the console logs for request and response details.

These examples provide a complete flow from service definition to client-server communication using the gRPC module.