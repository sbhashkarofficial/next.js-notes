# Caching

## Enabling Cache Components

To utilize caching, we must enable it our project's configuration file(`next.config.ts` or `.js`). To do this we set `cacheComponents: true` inside the configuration object. 

```ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  cacheComponents: true,
} 
export default nextConfig
```

## Usage

The `use cache` directive caches the return value of async functions and components. It can be applied at two levels:

1. **Data-level :** Cache a function that fetches or computes data.

2. **UI-level :** Cache an entire component or page

A smart feature of this directive is that any arguments passed to the function automatically become part of the 'cache key', which means different inputs will produce separate cache entries.

### Data-level caching

 Add the `use cache` directive at the top of the function body:

```ts
import {cacheLife} from 'next/cache'
export async function getUsers() {
    'use cache'
    cacheLife(hours);
    return db.query('SELECT * FROM users');
}
```

### UI-level caching

To cache and entire component, page, or layout, add the `use cachee` directive at the top of the component or page body:

```ts
import {cacheLife} from 'next/cache'
export default async function Page(){
    'use cache'
    cacheLife(hours);
    const users = await db.query('SELECT * FROM users');
    return (
        <ul>
          {users.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
    )
}
```

> cacheLife is a companion function to `'use cache'`. It allows to define a specific lifetime for the cached entries. Without a defined lifetime, the cache might never update, or it might update too frequently. Once the defined time expires, Next.js automatically handles the re-fetching or re-computing of that data in the background.

## Automatic Caching in Next.js

1. **Data fetching(via `fetch`) :** Next.js memoizes the result of `fetch` requests. On requesting the same data in multiple places across the application (or even on the same page), Next.js performs the network request once and serves the cached result for all subsequent calls.

2. **Static Site Generation (SSG):** During the build process, Next.js generates the HTML for the pages and caches them.

3. **Route Segment Caching :** Next.js caches the rendered output of individual "route segments" (the specific parts of your URL structure).

4. **Router Cache(Client Side) :** Next.js also maintains an automatic **Router Cache** in the browser. Next.js prefetches the RSC payload for the routes the user is likely to visit next.
