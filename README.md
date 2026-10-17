<h1 align="center">@crescendolab/zodios-core-zod-v4</h1>
<p align="center">
  <a href="https://github.com/ecyrbe/zodios">
    <img align="center" src="https://raw.githubusercontent.com/ecyrbe/zodios/main/docs/logo.svg" width="128px" alt="Zodios logo">
  </a>
</p>

<p align="center">
  A TypeScript API client with autocompletion and <a href="https://zod.dev/">zod</a> validation, backed by <a href="https://axios-http.com">axios</a>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@crescendolab/zodios-core-zod-v4">
    <img alt="npm version" src="https://img.shields.io/npm/v/@crescendolab/zodios-core-zod-v4.svg">
  </a>
  <a href="https://www.npmjs.com/package/@crescendolab/zodios-core-zod-v4">
    <img alt="npm downloads" src="https://img.shields.io/npm/dw/@crescendolab/zodios-core-zod-v4">
  </a>
  <a href="https://github.com/crescendolab-open/zodios-core-zod-v4/blob/main/LICENSE">
    <img alt="License" src="https://img.shields.io/github/license/crescendolab-open/zodios-core-zod-v4">
  </a>
  <img alt="CI" src="https://img.shields.io/github/actions/workflow/status/crescendolab-open/zodios-core-zod-v4/ci.yml?branch=main">
</p>

A [Crescendo Lab](https://cresclab.com/) fork of [`@zodios/core`](https://github.com/ecyrbe/zodios) that supports zod 4.

# Why this fork?

`@zodios/core` 10.9.6, released in August 2023, is the latest upstream release. Its peer dependency is `zod ^3.x`, so it can't type-check zod 4 schemas.

Crescendo Lab's apps define their APIs with zodios and are moving to zod 4. This fork keeps the `@zodios/core` 10.x API and changes only what zod 4 requires:

- Types and validation are built on zod 4 schemas, imported from `zod/v4`.
- The peer dependency accepts `zod ^3.25.49 || ^4.0.0`. Both release lines ship zod 4 under `zod/v4`.

It pairs with [`@crescendolab/zodios-react-query-v5`](https://github.com/crescendolab-open/zodios-react-query-v5), a fork of `@zodios/react` for React 19 and TanStack Query v5.

# Maintenance

This fork exists to meet Crescendo Lab's own needs. It is **not actively maintained for the long term**:

- We fix issues that affect our usage. Other issues and pull requests may get a slow response or none.
- We don't plan new features or follow upstream changes. The upstream v11 roadmap is out of scope.
- If upstream releases zod 4 support, or a maintained alternative fits your project, prefer that.

Pin the version you depend on and review changes before upgrading.

**Table of contents:**

- [Why this fork?](#why-this-fork)
- [Maintenance](#maintenance)
- [What is it?](#what-is-it)
- [Install](#install)
- [How to use it on client side ?](#how-to-use-it-on-client-side-)
  - [Declare your API with zodios](#declare-your-api-with-zodios)
  - [API definition format](#api-definition-format)
- [Full documentation](#full-documentation)
- [Ecosystem](#ecosystem)
- [Dependencies](#dependencies)
- [License](#license)

# What is it?

An axios-compatible API client with these features:

- Simple, centralized API declaration
- TypeScript autocompletion for URLs and parameters
- Typed responses
- Parameter and response schemas with zod
- Response schema validation
- Plugins, such as `fetch` adapters or automatic `auth` injection
- All axios features
- React hooks through [`@crescendolab/zodios-react-query-v5`](https://github.com/crescendolab-open/zodios-react-query-v5)

# Install

```bash
> pnpm add @crescendolab/zodios-core-zod-v4 axios zod
```

or

```bash
> npm install @crescendolab/zodios-core-zod-v4 axios zod
```

Zodios requires zod 4 schemas:

- With `zod@^4.0.0`, import from `zod` or `zod/v4`.
- With `zod@^3.25.49`, import from `zod/v4`. In this release line, `zod` exports zod 3 schemas, which don't type-check with this package.

# How to use it on client side ?

For an almost complete example on how to use zodios and how to split your APIs declarations, see the [dev.to example](examples/dev.to/).

## Declare your API with zodios

Here is an example of API declaration with Zodios.
  
```typescript
import { Zodios } from "@crescendolab/zodios-core-zod-v4";
import { z } from "zod/v4";

const apiClient = new Zodios(
  "https://jsonplaceholder.typicode.com",
  // API definition
  [
    {
      method: "get",
      path: "/users/:id", // auto detect :id and ask for it in apiClient get params
      alias: "getUser", // optional alias to call this endpoint with it
      description: "Get a user",
      response: z.object({
        id: z.number(),
        name: z.string(),
      }),
    },
  ],
);
```

Calling this API is now easy and has builtin autocomplete features :  
  
```typescript
//   typed                     auto-complete path   auto-complete params
//     ▼                               ▼                   ▼
const user = await apiClient.get("/users/:id", { params: { id: 7 } });
console.log(user);
```
  
It should output  
  
```js
{ id: 7, name: 'Kurtis Weissnat' }
```
You can also use aliases :
  
```typescript
//   typed                     alias   auto-complete params
//     ▼                        ▼                ▼
const user = await apiClient.getUser({ params: { id: 7 } });
console.log(user);
```
## API definition format

```typescript
type ZodiosEndpointDescriptions = Array<{
  method: 'get'|'post'|'put'|'patch'|'delete';
  path: string; // example: /posts/:postId/comments/:commentId
  alias?: string; // example: getPostComments
  immutable?: boolean; // flag a post request as immutable to allow it to be cached with react-query
  description?: string;
  requestFormat?: 'json'|'form-data'|'form-url'|'binary'|'text'; // default to json if not set
  parameters?: Array<{
    name: string;
    description?: string;
    type: 'Path'|'Query'|'Body'|'Header';
    schema: ZodSchema; // you can use zod `transform` to transform the value of the parameter before sending it to the server
  }>;
  response: ZodSchema; // you can use zod `transform` to transform the value of the response before returning it
  status?: number; // default to 200, you can use this to override the success status code of the response (only useful for openapi and express)
  responseDescription?: string; // optional response description of the endpoint
  errors?: Array<{
    status: number | 'default';
    description?: string;
    schema: ZodSchema; // transformations are not supported on error schemas
  }>;
}>;
```
# Full documentation

This fork keeps the `@zodios/core` 10.x API, so the [upstream documentation](https://www.zodios.org) applies. Replace `@zodios/core` with `@crescendolab/zodios-core-zod-v4` and define schemas with zod 4.

- [API definition](https://www.zodios.org/docs/category/zodios-api-definition)
- [Http client](https://www.zodios.org/docs/category/zodios-client)

# Ecosystem

- [`@crescendolab/zodios-react-query-v5`](https://github.com/crescendolab-open/zodios-react-query-v5): React hooks for this fork, built on TanStack Query v5

The upstream packages below depend on `@zodios/core` and zod 3. They haven't been tested with this fork:

- [openapi-zod-client](https://github.com/astahmer/openapi-zod-client): generate a zodios client from an OpenAPI specification
- [@zodios/express](https://github.com/ecyrbe/zodios-express): end-to-end type safety like tRPC, but for REST APIs
- [@zodios/plugins](https://github.com/ecyrbe/zodios-plugins): plugins for zodios
- [@zodios/solid](https://github.com/ecyrbe/zodios-solid): a solid-query wrapper for zodios

# Dependencies

Zodios even when working in pure Javascript is better suited to be working with Typescript Language Server to handle autocompletion.
So you should at least use the one provided by your IDE (vscode integrates a typescript language server)
However, we will only support fixing bugs related to typings for TypeScript v4.5 and later
Earlier versions should work, but do not have TS tail recursion optimization that impact the size of the API you can declare.

Also note that Zodios does not embed any dependency. It's your Job to install the peer dependencies you need.  
  
Internally Zodios uses these libraries on all platforms :
- zod
- axios

# License

Forked from [ecyrbe/zodios](https://github.com/ecyrbe/zodios), which is licensed under the [MIT License](https://github.com/ecyrbe/zodios/blob/main/LICENSE).

Copyright © 2025-present Crescendo Lab Inc.

Licensed under the [Apache License, Version 2.0](https://github.com/crescendolab-open/zodios-core-zod-v4/blob/main/LICENSE).
