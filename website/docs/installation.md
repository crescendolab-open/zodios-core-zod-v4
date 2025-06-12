---
sidebar_position: 2
---

# Installation

:::info
Zodios packages can be installed using your preferred package manager.  
We only show npm and pnpm as they are the most popular.
:::

## Frontend installation

```bash npm2yarn
npm install @crescendolab/zodios-core-zod-v4 axios zod
```
### With React

if you want to use the react hooks, you need to install the following packages:

```bash npm2yarn
npm install @tanstack/react-query @crescendolab/zodios-core-zod-v4 @zodios/react axios react react-dom zod
```

### With Solid

if you want to use the solid hooks, you need to install the following packages:

```bash npm2yarn
npm install @tanstack/solid-query @crescendolab/zodios-core-zod-v4 @zodios/solid axios solid-js zod
```
### Install type definitions

install those even in javascript projects.

```bash npm2yarn
// if you use react
npm install --dev @types/react @types/react-dom
```

## Backend installation

### With Express

```bash npm2yarn
npm install @crescendolab/zodios-core-zod-v4 @zodios/express express zod axios
```

optional packages for openapi generation:

```bash npm2yarn
npm install @zodios/openapi swagger-ui-express
```

### With NextJS

```bash npm2yarn
npm install @crescendolab/zodios-core-zod-v4 @zodios/express next zod axios react react-dom
```

### Install type definitions

install those even in javascript projects

```bash npm2yarn
// if you use express
npm install --dev @types/express
// or with next.js
npm install --dev @types/express @types/react @types/react-dom
```
