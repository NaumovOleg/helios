---
sidebar_position: 1
---

# Installation & Setup

Let's set up a new HeliosJS project from scratch.

## Prerequisites

Make sure you have Node.js installed:

```bash
node --version
# Should be v20.0.0 or higher
```

## Step 1: Create a New Project

Create a new directory and initialize a Node.js project:

```bash
mkdir my-helios-app
cd my-helios-app
npm init -y
```

Or with yarn:

```bash
yarn init -y
```

## Step 2: Install Required Packages

Install the core packages and reflect-metadata:

```bash
npm install @heliosjs/core @heliosjs/http reflect-metadata
```

With yarn:

```bash
yarn add @heliosjs/core @heliosjs/http reflect-metadata
```

### What Each Package Does

| Package          | Purpose                                    |
| ---------------- | ------------------------------------------ |
| @heliosjs/core   | Core decorators and dependency injection   |
| @heliosjs/http   | HTTP server, routing, and request handling |
| @heliosjs/aws    | Aws adapters                               |
| reflect-metadata | Required for TypeScript decorators to work |

## Step 3: Install TypeScript

Since HeliosJS is built with TypeScript, let's install it:

```bash
npm install -D typescript @types/node ts-node
```

With yarn:

```bash
yarn add -D typescript @types/node ts-node
```

## Step 4: Configure TypeScript

Create a tsconfig.json file in your project root:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "strictPropertyInitialization": false
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Important Options

- `experimentalDecorators`: true — Enables decorator support
- `emitDecoratorMetadata`: true — Required for dependency injection
- `strictPropertyInitialization`: false — Helps with class properties

## Step 5: Create Project Structure

Create the following folder structure:

```
my-helios-app/
├── src/
│   ├── controllers/
│   │   └── health.controller.ts
│   ├── app.ts
│   └── index.ts
├── package.json
├── tsconfig.json
└── .gitignore
```

Create a .gitignore file:

```
node_modules/
dist/
.env
```

## Step 6: Create Your First App

Create `src/controllers/health.controller.ts`:

```typescript
import "reflect-metadata";
import { Controller, Response, Response } from "@heliosjs/core";

@Controller("/health")
export class HealthController {
  @Get("/")
  check() {
    return { status: "ok", timestamp: new Date().toISOString() };
  }
}
```

Create `src/server.ts`:

```typescript
import { HealthController } from "./controllers/health.controller.ts";
import { Server } from "@heliosjs/http";
@Server({ controllers: [HealthController] })
export class App {}
```

Create `src/index.ts`:

```typescript
import { App } from "./app";
import { Helios } from "@heliosjs/http";

const server = new Helios(App);
server.listen(3000);
```

## Step 7: Add Package Scripts

Update your `package.json` with scripts:

```json
{
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js",
    "dev": "ts-node src/index.ts",
    "watch": "ts-node-dev src/index.ts"
  }
}
```

## Step 8: Run Your App

Start the development server:

```bash
npm run dev
```

Or with yarn:

```bash
yarn dev
```

You should see:

```
🚀 Server is running on http://localhost:3000
```

## Step 9: Test Your API

Open another terminal and test the health endpoint:

```bash
curl http://localhost:3000/health
```

Expected response:

```json
{
  "status": "ok",
  "timestamp": "2024-01-15T10:30:00.000Z"
}
```

## Troubleshooting

### "Cannot find module 'reflect-metadata'"

Make sure you import reflect-metadata at the very top of your entry file:

```typescript
import "reflect-metadata"; // Must be first!
```

### Decorators not working

Check your tsconfig.json has:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

### Port already in use

Change the port in `app.ts`:

```typescript
await this.server.listen(3001); // Use a different port
```

## Next Steps

Now that your project is set up, let's create your first real controller!

Next: Your First Controller
