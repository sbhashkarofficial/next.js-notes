# Headers

It is an async function (must be used with async/await) that allows us to read the HTTP incoming headers (from client) within Server Components.

`headers` is a request-time APIs (function that access request-specific data).
This causes a component to opt into dynamic rendering. This means it will no longer be treated as a 'static' route and will not be pre-rendered.

```ts
import {headers} from 'next/headers';

export default async function Page(){
    const headersList = await headers();
    return /* ... */
}
```

`headers` does not take any parameters.

## Returns

It returns a read-only Web Headers object.

### Methods

1. **`.entries()`** : 
   return an iterator allowing to go through all key/value pairs contained in this object.

2. **`.keys()`** : 
    returns an iterator allowing us to go through all keys of key/value pairs contained in the object.

3. **`.values()`** :
   returns an iterator allowing us to go through all values of key/value pairs contained in the object.

4. **`.forEach(callbackFn, ?thisArg)`**:
   executes a provided function once for each key/value pair in the object.

5. **`.get(name)`** :
   `name` is the the name of the HTTP header whose value is to be retrieved from the `Headers` object.
   This method returns a string sequence representing the values of the retrieved header or `null` if this header is not set.
   
   > Because HTTP headers are case-insensitive (e.g., `'User-Agent'` and `'user-agent'` are treated as the same header)

6. **`.has(name)`** :
   It returns a boolean stating whether the specified header exists in the object.
