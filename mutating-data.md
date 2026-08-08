# Mutating Data

It involves creating, updating and deleting data(server-side mutations).

## Server Functions

A Server Function is an asynchronous function that executes exclusively on the server. These functions can be called from the client via a network request.

To define a Server Function, `'use server'` directive is used. There are two ways to do it: 

1. **At the top of a file :** It marks every exported function in that file as a Server Function. 
2. **At the top of the function body inside an async  Server Component :** This makes that function only a Server Function.

### How they Work

- These function can be invoked only using HTTP `POST` requests.

- When an action is invoked, Next.js can process the data mutation and return both the new data and the updated UI back to the client in a single server roundtrip.

> A Server Action is a Server Function used specifically for handling form submissions and data mutations. 

```ts
'use server'

import { auth } from '@/lib/auth'

export async function createPost(formData: FormData) {
  const session = await auth()
  if (!session?.user) {
    throw new Error('Unauthorized')
  }

  const title = formData.get('title')
  const content = formData.get('content')

  // Mutate data
  // Revalidate cache
}
```

### Invoking Server Functions

We can import a Server Function into a Client Component, and can use `async/await` function to invoke it. When we write `await aServerAction(data)`, Next.js intetcepts that call, it automatically packages the data and securely sends an HTTP `POST` request to the server.

#### Using Event Handlers

```ts
'use server'
export async function deletePost(){
    //authorisation logic
    //database logic
    return {success: true};
}
```

```ts
'use client'
import {createPost} from '@/app/actions'

export default function DeletePostButton(){
    const handleClick = ()=> {
        const result = await deletePost('post_123', 'user_001');
        //try-catch should be used
    }
    return (
        <button onClick={handleClick}>
          Delete Post
        </button>
    ) 
}
```

#### useEffect

We can use the React `useEffect` hook to invoke a ServerAction when the component mounts or dependency changes.

#### In React form component

React extends the HTML `<form>` element to allow a Server Function to be invoked with the HTML `action` prop.

When invoked in a form, the function automatically receives the `FormData` object.

```ts
import {createPost} from '@/app/actions' //importing a server action
export function Form(){
    return (
        <form action={createPost}>
            <input type="text" name="title" />
            <input type="text" name="content" />
            <button type="submit">Create</button>
        </form>
    )
}
```

### Some Important Points

1. The `refresh()` function is used to refresh the client-side router from the server side.
   `import { refresh } from 'next/cache'`
   When called after a mutation, it sends a signal back to the browser to refresh the client router. This updates the UI with the latest state without causing a hard browser reload, thereby preserving the user's active client-side state.

2. Next.js caches almost everything by default for fast ux. When we make data mutations, we need to tell Next.js that to throw away the outdated data and fetch fresh data. To do so we can simply call `revalidatePath('/posts')` after database mutation is complete. The updated UI is sent back in the exact same response.

3. After data mutations, to send the user to a new page, we can use `redirect(/next-route)` function. Under the hood, `redirect` function actually throws a special JavaScript error to stop the server from executing any more code and instantly trigger the navigation.

4. We can `get`, `set`, and `delete` cookies inside a Server Action using the **cookies API.** When we set or delete a cookie in a Server Action, Next.js re-renders the current page and its layouts on the server so the UI reflects the new cookie value.
