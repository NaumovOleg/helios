---
sidebar_position: 12
---

# AWS Lambda Integration with HeliosJS

This document provides a detailed overview and example of integrating HeliosJS with AWS Lambda using the `LambdaAdapter`. The example demonstrates how to use a controller, plugin, and adapter to expose a Lambda handler suitable for AWS Lambda environments.

---

## Example: Lambda Integration

```typescript
import { LambdaAdapter, Plugin } from "@heliosjs/aws";
import { Any, Controller } from "@heliosjs/core";

@Controller({ prefix: "metric" })
export class MetricsController {
  @Any()
  async any(@Req() resp: any) {}
}

const lambdaAdapter = new LambdaAdapter(MetricsController);
export const handler = lambdaAdapter.handler;
```

---

### Overview

This example shows how to create a Lambda handler using HeliosJS's AWS Lambda integration utilities.

- **LambdaAdapter**: This class adapts HeliosJS controllers and plugins to AWS Lambda's event-driven model. It exposes a `handler` function compatible with AWS Lambda.
- **MetricsController**: A simple controller with a prefix `metric` and an `Any` route handler method. The `Any` decorator allows this route to handle any HTTP method.

- Instantiates a `LambdaAdapter` with the `MetricsController`.
- Exposes the Lambda-compatible `handler` function from the adapter.

### Usage Notes

- The exported `handler` can be used directly as the AWS Lambda function handler.
- The adapter handles the translation between AWS Lambda events and HeliosJS's controller/plugin architecture.
- Plugins allow extending the server lifecycle and adding middleware-like hooks.
- Controllers define routes and handlers similarly to traditional HTTP servers but adapted for Lambda.

---

This setup provides a clean and modular way to build AWS Lambda functions using HeliosJS, leveraging its controller and plugin system for maintainable and scalable serverless applications.
