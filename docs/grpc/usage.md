# gRPC Module Usage

This document provides detailed instructions on how to use the gRPC module, including defining gRPC services, implementing gRPC methods, injecting gRPC clients, and registering services on the server.

## Defining gRPC Services

Use the `@GrpcService` decorator to define a class as a gRPC service. This decorator marks the class so that the gRPC server can recognize and register it.

```typescript
import { GrpcService, GrpcMethod } from '@yourorg/grpc';

@GrpcService()
class HeroService {
  @GrpcMethod('HeroService', 'FindOne')
  findOne(data: { id: number }) {
    return { id: data.id, name: 'Hero ' + data.id };
  }
}
```

In this example, `HeroService` is a gRPC service with a method `findOne` that handles the `FindOne` RPC call.

## Implementing gRPC Methods

Use the `@GrpcMethod` decorator on methods inside your service class to define gRPC methods. The decorator takes the service name and method name as parameters.

```typescript
@GrpcMethod('HeroService', 'FindOne')
findOne(data: { id: number }) {
  return { id: data.id, name: 'Hero ' + data.id };
}
```

This method will be called when the `FindOne` RPC is invoked on the `HeroService`.

## Injecting gRPC Clients

To use a gRPC client in your services or controllers, use the `@InjectGrpcClient` decorator to inject a client instance by its name.

```typescript
import { InjectGrpcClient, GrpcClient } from '@yourorg/grpc';

class HeroClientService {
  @InjectGrpcClient('hero')
  private readonly client!: GrpcClient;

  async findOne(id: number) {
    return this.client.send('HeroService.FindOne', { id });
  }
}
```

This example shows how to inject a client named `hero` and use it to send a request to the `FindOne` method.

## Registering Services and Starting the gRPC Server

To register your services and start the gRPC server, configure the `GrpcModule` with the server options and include your service classes.

```typescript
import { GrpcModule } from '@yourorg/grpc';

const grpcModule = GrpcModule.forRoot({
  server: {
    port: 50051,
  },
  clients: [],
});

// Register your service classes with the module or application container
// Start the server (implementation depends on your application bootstrap)
```

The server will listen on the specified port and handle incoming gRPC requests routed to your registered services.

---

For more examples, see the `examples/src/grpc/server.ts` and `examples/src/grpc/client.ts` files in the project repository.