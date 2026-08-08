# Fetching Data

## Server Components

### With the fetch API

To fetch data with the `fetch` API, turn the component into an asynchronous function, and await the fetch call. For example: 

```ts
export default async function Page(){
    const response = await fetch('https://api.example.com');
    const data = await data.json();
    return (
        //use data as required
    )
}
```

- <u>Identical `fetch` requests in a React component tree are [memoized](https://nextjs.org/docs/app/glossary#memoization) by default, so we can fetch data in the component that needs it instead of drilling props.</u>

- <u>`fetch` requests are not cached by default and will block the page from rendering until the request is complete. Use the `use cache`directive to cache results, or wrap the fetching component in `<Suspense>` to stream fresh data at request time.</u>

### With an ORM or database

Since Server Components are rendered on the server, credentials and query logic will not be included in the client bundle so it is safe to make database queries using an ORM or database client.

```ts
//to be included later
```

#### Streaming

When we fetch data in Server Components, the data is fetched and rendered on the server for each request. We can progressively send data using smaller chunks. 

There are two ways of using streaming:

1. Using a loading.tsx file in the same folder where the page is.

2. Wrapping a component with `<Suspense>`.

## Client Components

There are two ways to fetch data in Client Components, using:

1. React's use API

2. ReatQuery library

3. using useEffect 
   
   We can't use `await` directly inside the Client Component because it cannot be an async function unliike a Server Component.

### Streaming data with the `use` API

#### React's use API

We start by fetching data from the Server Component and pass the promise down to a Client Component as a prop. Also the Client Component is wrapped using `<Suspense>` boundaries.

```ts
import ClientComponent from './index.ts'
import {Suspense} from 'react'
export defautl function Page(){
    //we don't await the promise
    const response = fetch('api.example.com').then((data)=>data.json());
    return (
        <div>
            <Suspense fallback={<p>Loading Data...</p>}>
                <ClienComponent response={response}/>
            </Suspense>
        </div>
    )
}
```

```ts
'use client'
import {use} from 'react'

export default function Page(
     {response}: {response: Promise<any[]>}
){
    const data = use(response);
    return (
        //{data.map()...}
    )
}
```

#### Using React Query

## Sequential Data Fetching

This involves fetching data one after another. It is useful when the when the later requests depend on the former ones.

```ts
import PostCard from './index.ts'
export default async function Page({params}:{
    params: Promise<{username: string}>
}){
    const {username} = await params;
    const userData = await fetch(`api.example.com/user/${username}`)
        .then(res => res.json());    
    return (
        <>
          {/* Show fallback UI while the Post component is loading */}
              <Suspense fallback={<div>Loading...</div>}>
              {
                <PostsList channelId={userData.channelId}/>
              }      
             </Suspense>
        </>
    )
} 

async function PostsList({channelId}: {channelId: string}){
    const posts = await fetch(`api.example/.com/posts/${channelId}`)
        .then(res => res.json());
     return (
        <div>
            {posts.map(post => <PostCard key={post.id} post={post}/>)}
        </div>
    )
}
```

`<Suspense>` allows the `posts` to stream in after the `userData` loads. However, the page still waits for the `userData` before displaying anything. To prevent this, we can wrap the entire page component in a `<Suspense>` boundary (for example, using a  `loading.ts` file to show a loading state immediately.

## Parallel Data Fetching

It involves starting data requests in a route at the same time.

 Start multiple requests by calling `fetch`, then await them with `Promise.all`. Requests begin as soon as `fetch` is called.

This is used when one request doesn't depend on any other.

```ts
export default async function Page({
  params,
}: {
  params: Promise<{ username: string }>
}) {
  const { username } = await params

  // Initiate requests
  const artistData = await fetch(`https://api.example.com/artist/${username}`)
    .then((res)=> res.json());
  const albumsData = await fetch(`https://api.example.com/artist/${username}/albums`)
    .then((res)=> res.json());

  const [artist, albums] = await Promise.all([artistData, albumsData])

  return (
    <>
      <h1>{artist.name}</h1>
      <Albums list={albums} />
    </>
  )
}
```
