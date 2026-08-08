# The cookies API

The `cookies` is an asynchronous function that returns a promise, so it must be used within `async` functions using `await` keyword. 

`cookies` is a request-time APIs (function that access request-specific data).
This causes a component to opt into dynamic rendering. This means it will no longer be treated as a 'static' route and will not be pre-rendered.

The `cookies()` function :

1. In **Server Components**, allows us to read the cookies sent by the browser in the incoming HTTP request. 
   Because this runs on the server, it can access `HttpOnly` cookies, which are invisible to client-side JavaScript.

2. In **Server Actions** and **Route Handlers**, allows us to **read/write/delete** cookies.

```ts
// a server component
import { cookies } from 'next/headers'
export default async function Page(){
    const cookieStore = await cookies();
    const theme = cookieStore.get('theme');
    return /*.........*/
}
```

## Method

1. `.get('name')` :
    returns an object containing the cookie's `'name'` and `value` if found, or `undefined` otherwise.

2. `.getAll()` : 
   returns an array of all cookies present in the incoming request.

3. `.has('name')` :
   returns a boolean based on if the provided cookie exists.

4. `.set(name, value, ?options)`
   accepts a cookie name, value, and options and instructs the client to store or update a cookie. This method modifies the `Set-Cookie` header in the outgoing HTTP response, this method can only be used within **Server Components** and **Route Handlers**.

5. `.delete(name)` : accepts a cookie name and instructs the client to delete the cookie. This method can only be used within Server Components** and **Route Handlers**.

## Options

When setting a cookie, the following properties from the `options` object are supported:

| Property      | Type                                     | Value                                                           |
| ------------- | ---------------------------------------- | --------------------------------------------------------------- |
| `expires`     | Date                                     | defines the exact date when the cookie will expire.             |
| `maxAge`      | Number                                   | defines the cookie's lifespan in seconds.                       |
| `domain`      | String                                   | specifies the domain where the cookie is available.             |
| `path`        | String, default ('/')                    | limits the cookie's scope to a specific path within the domain. |
| `secure`      | Boolean                                  | ensures the cookie is sent only over HTTPS connections.         |
| `httpOnly`    | Boolean                                  | restricts the browser's JavaScript to access the cookie.        |
| `sameSite`    | `'lax'` , `'strict'` , `'none'`          | controls the cookie's cross-side access                         |
| `priority`    | String (`'low'` , `'medium'` , `'high'`) | specifies the cookie's priority                                 |
| `partitioned` | Boolean                                  |                                                                 |

There are three ways to delete a cookie:

1. using the `delete()` method.

2. setting a new cookie with the same name and an empty value.

3. setting the `maxAge` to 0 will immediately expire a cookie.

```ts
'use server'
import {cookies} from 'next/headers'
export async function deleteCookie(data){
    const cookieStore = await cookies();
    //1.
    cookieStore.delete('name');
    //2.
    cookieStore.set('name', '');
    //3.
    cookieStore.set('name', 'value', {maxAge: 0});
}
```
