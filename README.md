<p align="center">
  <h1 align="center">StateMesh</h1>
  <p align="center">
    <strong>Transaction-first state orchestration for React</strong>
  </p>
  <p align="center">
    One store. One type system. Zero runtime dependencies.
  </p>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/react-statemesh"><img src="https://img.shields.io/npm/v/react-statemesh/latest?color=blue&label=npm&logo=npm" alt="npm version"></a>
  <a href="https://www.npmjs.com/package/react-statemesh"><img src="https://img.shields.io/npm/dm/react-statemesh?color=blue&logo=npm" alt="downloads"></a>
  <img src="https://img.shields.io/bundlephobia/minzip/react-statemesh?color=blue&label=bundle%20size&logo=bundlephobia" alt="bundle size">
  <img src="https://img.shields.io/badge/built_with-TypeScript-3178c6?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/tests-603_passing-brightgreen?logo=vitest&logoColor=white" alt="tests">
  <img src="https://img.shields.io/badge/dependencies-0-brightgreen" alt="zero dependencies">
  <img src="https://img.shields.io/badge/React-%3E%3D18-61dafb?logo=react&logoColor=white" alt="React 18+">
</p>

---

## The Problem

React apps scatter state across too many libraries. Redux for global state. React Query for server state. React Router for navigation. Formik or React Hook Form for forms. Undo libraries. Persistence layers. Cross-tab sync. Each one has its own API, its own types, its own DevTools, and its own way of doing things.

StateMesh replaces all of that with **one store, one set of types, one DevTools panel**.

## What You Get

<table>
<tr>
<td width="50%">

**State Management**
- External store with path-based subscriptions
- Named actions with draft mutation
- Computed state with dependency tracking
- Batch operations for grouped updates
- Undo/redo with configurable history
- State time travel with ring buffer

</td>
<td width="50%">

**Async Transactions**
- Full lifecycle: validate, optimistic update, effect, commit, rollback
- Retry with exponential backoff and jitter
- Timeout and cancellation via AbortSignal
- Concurrency policies: takeLatest, block, queue
- Status tracking and logging

</td>
</tr>
<tr>
<td>

**Server State**
- Resource cache with request deduplication
- Stale-while-revalidate and polling
- Invalidation tags and refetch triggers
- Pagination and infinite scroll
- Mutations with optimistic rollback
- Offline mutation queueing

</td>
<td>

**Routing**
- Built-in router where routing IS state management
- Nested routes with lazy loading
- Route loaders, guards, and middleware
- Navigation rollback on loader failure
- Keep-alive pools with LRU eviction
- Predictive prefetch and shared element transitions

</td>
</tr>
<tr>
<td>

**Forms**
- Async field-level validation
- Schema adapters (Zod, Yup, Standard Schema)
- Server error mapping
- Dirty tracking and autosave
- Field arrays and multi-step wizards
- Checkbox, radio, select, file helpers

</td>
<td>

**Platform**
- Persistence (localStorage, sessionStorage, IndexedDB)
- Cross-tab sync (BroadcastChannel, localStorage fallback)
- URL state with custom param names
- DevTools with timeline, profiler, and diagnostics
- Testing utilities with mocks and async helpers
- Full SSR and Next.js App Router support

</td>
</tr>
</table>

## Quick Start

```bash
npm install react-statemesh
```

```tsx
import { createMesh, StateMeshProvider, useMeshState } from "react-statemesh";

const mesh = createMesh({
  state: { count: 0 }
});

function Counter() {
  const [count, setCount] = useMeshState<number>("count");
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}

export function App() {
  return (
    <StateMeshProvider mesh={mesh}>
      <Counter />
    </StateMeshProvider>
  );
}
```

State reads use `useSyncExternalStore`. Updating `count` does not rerender components that don't read it.

## How It Works

Every state change in StateMesh is a **transaction**. This is the core idea. Whether it's a button click, an API call, a form submission, or a route transition, it goes through the same lifecycle:

```
Action/Mutation/Transaction
  └─ before()        → validate preconditions
  └─ optimistic()    → update UI immediately
  └─ effect()        → call API, write to disk, etc.
  └─ commit()        → apply the real result
  └─ rollback()      → undo the optimistic update on failure
  └─ retry           → exponential backoff with jitter
```

This means you get **optimistic UI, error recovery, and rollback for free** on every async operation. Not just API calls — form submissions, route transitions, and custom workflows too.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    React Components                  │
│  useMeshState · useMeshResource · useMeshForm · ...  │
└──────────────────────┬──────────────────────────────┘
                       │ useSyncExternalStore
┌──────────────────────┴──────────────────────────────┐
│                     StateMesh Store                  │
│  state · actions · computed · transactions · forms   │
│  resources · mutations · url state · undo/redo       │
├─────────────────────────────────────────────────────┤
│  Middleware · Guards · Pipelines · Event System      │
├─────────────────────────────────────────────────────┤
│  Persistence · Tab Sync · Router · DevTools          │
└─────────────────────────────────────────────────────┘
```

Subscriptions are **path-scoped and equality-checked**. Updating `cart.items` does not rerender components reading `theme`. Computed values cache until their dependencies change. Resources deduplicate in-flight requests by key.

## Why StateMesh

| | StateMesh | Redux + RTK Query | Zustand + React Query | Jotai |
|---|---|---|---|---|
| **Libraries needed** | 1 | 3-4 | 2-3 | 2-3 |
| **Transactions** | Built-in | Manual | Manual | No |
| **Optimistic UI** | Automatic | Manual | Plugin | No |
| **Rollback** | Automatic | Manual | Plugin | No |
| **Router** | Built-in | Separate | Separate | Separate |
| **Forms** | Built-in | Separate | Separate | Separate |
| **Persistence** | Built-in | Plugin | Plugin | Plugin |
| **Undo/Redo** | Built-in | Plugin | No | No |
| **Time Travel** | Built-in | Plugin | No | No |
| **Cross-tab Sync** | Built-in | No | No | No |
| **Runtime deps** | 0 | 4+ | 2+ | 1+ |
| **TypeScript** | First-class | First-class | First-class | First-class |

## Documentation

- **[README](https://github.com/prakashaditya13/react-statemesh#readme)** — Full API reference with examples
- **[CHANGELOG](https://github.com/prakashaditya13/react-statemesh/blob/master/CHANGELOG.md)** — Version history
- **[CONTRIBUTING](https://github.com/prakashaditya13/react-statemesh/blob/master/CONTRIBUTING.md)** — Development setup and workflow
- **[SECURITY](https://github.com/prakashaditya13/react-statemesh/blob/master/SECURITY.md)** — Vulnerability reporting policy

## Packages

| Package | Description | Size |
|---------|-------------|------|
| `react-statemesh` | Core store, hooks, actions, transactions, forms, URL state, resources | 36 KB |
| `react-statemesh/router` | Built-in router with nested routes, loaders, guards, middleware | 36 KB |
| `react-statemesh/devtools` | DevTools dock with timeline, profiler, and diagnostics | 48 KB |
| `react-statemesh/persist` | Persistence adapters (localStorage, sessionStorage, IndexedDB) | 4 KB |
| `react-statemesh/sync` | Cross-tab sync (BroadcastChannel, localStorage fallback) | 4 KB |
| `react-statemesh/resources` | API client with auth refresh, retry, and upload support | 4 KB |
| `react-statemesh/testing` | Test helpers, mock utilities, and async assertions | 8 KB |

## Community

- **[Issues](https://github.com/prakashaditya13/react-statemesh/issues)** — Bug reports and feature requests
- **[Discussions](https://github.com/prakashaditya13/react-statemesh/discussions)** — Questions and ideas
- **[Security Advisories](https://github.com/prakashaditya13/react-statemesh/security/advisories)** — Report vulnerabilities privately

## License

[MIT](https://github.com/prakashaditya13/react-statemesh/blob/master/LICENSE) — free for personal and commercial use.
