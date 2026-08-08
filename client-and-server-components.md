# Server Component

A server component is rendered exclusively on the server. Next.js does not ship any of the component's JavaScript code to the client's browser. This results in faster loading times. By default, layouts and pages are server components.

# Client Component

It is rendered in the user's browser. These are necessary to add interactive features, such as state, effects, event listeners, etc. Also useful when we need Browser-only APIs like localStorage, window. In this case, the component's JavaScript is sent to the user's browser.

To make a component a Client Component, `'use client'` directive is added at the very top of the file. Once a file is marked with `"use client"`, **all of its imports and the components it directly renders are included in the client bundle**. This means we don’t need to add the directive to every component that is intended for the client. It does not apply to Server Components passed as children or other props.

> A great mental model is to build the "shell" and data-fetching parts of your app using Server Components, and then sprinkle in small, isolated Client Components only where the user actually needs to click, type, or interact.

## The React Server Component(RSC) Payload

The RSC Payload is a compact, specialized, JSON-like string format that the server sends to the browser after rendering the server components. It comprises of: 

1. The rendered UI of Server Components.

2. Placeholders for where Client Components should be rendered and references to their JavaScript files.

3. Any props passed from a Server Component to a Client Component

### On the client (first load):

1. HTML is used to show a non-interactive preview of the route.

2. RSC Payload is used to reconcile the Client and Server Components.

3. JavaScript is used to hydrate Client Components.

> **Hydration** is React's process for attaching event handlers to the DOM to make the static elements interactive.

### On subsequent Navigations:

- The RSC Payload is prefetched and cached for instant navigation.

- Client Components are rendered entirely on the client.

## Server Components inside Client  Components

We cannot directly import a Server Component into a file marked with `'use client'`. Next.js forces all of its direct imports to also become  Client Components. 

However, we can wrap Server Components inside Client Components by passing them as props - most commonly the `children` prop. In this case, the Client Component receives a pre-rendered React Node from the server and fit it in place.

React context is not supported in Server Components. To use context, create a Client Component that accepts `children` and then import it into a Server Component.

```ts
//client component
'use client'
import { createContext } from 'react'
export const ThemeContext = createContext({});
export default function ThemeProvider({children}: {
    children: React.ReactNode
}) {
    return (
        <ThemeContext.Provider value = "dark"> {children} </ThemeContext.Provider>
    )
}
```

```ts
//server component
import ThemeProvider from './index.ts'
export default function RootLayout({
    children,
}: {
    children: React.ReactNode
}) {
    return (
        <html>
            <body>
                <ThemeProvider> {children} </ThemeProvider>
            </body>
        </html>
    )
}
```

## Third Party Components within a Server Component

To use third party components within a Server Component, we need to wrap it inside a Client Component(as it may contain client-only features).

```ts
//client component
'use client'
import {Carousedl} from 'acme-carousel'
export default Carousel
```

```ts
//server component
import Carousel from './index,ts'
export default function Page(
    return (
        <div>
            <Carousel/>
        </div>
    )
)
```

## Prevent Environment Poisoning

JavaScript modules can be shared between both Server and Client Components modules. This means it's possible to accidentally import server-only code into the client. 
In Next.js, only environment variables prefixed with `NEXT_PUBLIC_` are included in the client bundle.
To prevent accidental usage in Client Components, we can use the server-only package and then import the package into a file that contains server-only code: 
`import 'server-only'`. Now, if we try to import the module into a Client Component, there will be a build-time error.

Similarly `client-only` can also be used to prevent unintentional import of this Client Component inside a Server Component.
