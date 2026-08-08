# Dynamic Route Segments

A URL path is a sequence of path segments. In the App Router, a segment may be **static** (a literal value matched exactly) or **dynamic** (a placeholder that captures a value from the URL).

## Dynamic Segments

### Single Parameter

A dynamic segment can be created by wrapping a folder's name in square brackets:
 `[folderName]`. These are passed as a `params` prop to page, layout, route, and loading, error, etc. components of the segment.

#### params

It is a promise.

##### In Client Components

Params can be accessed using the `use` API.

```tsx
//app/blog/[slug]/page.tsx
`use client`
import {use} from 'react'

export default function Page({params}: {
    params: Promise<{slug: string}>
}) {
    const {slug} = use{params}
    return /*....*/
}
```

##### In Server Components

We can use `async/await` to handle params:

```tsx
//app/blog/[slug]/page.tsx
export default async function Page({params}: {
    params: Promise<{slug: string}>
}) {
    const {slug} = await params;
    return /*....*/
}
```

### Catch-All

### Optional Catch-all

## Search Params

These are query strings that allow us to access dynamic information that appears in the URL after the question mark(?). Search params are optional, key-value pairs that are not part of the route structure but are appended to the end of the URL.

Example: `/segment1/segment2?key1=value1&key2=value2`

### In Server Components

**`searchParams`** is a promise that is passed as a prop **only** to the page component of the active route segment.

```tsx
export default async function Page({searchParams}: {
    searchParams: Promise<{[key: string]: string | string[] | undefined}>
}){
    const query = await searchParams;
}
```

### In Client Components

We can use React's `use` hook to access `searchParams`.

```tsx
'use client'
import {use} from 'react'

export default function Page({searchParams}: {
    searchParams: Promise<{[key: string]: string | string[] | undefined}>
}){
    const query = use(searchParams);
}
```
