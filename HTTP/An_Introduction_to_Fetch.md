# An Introduction to Fetch
### Or, "Go get it, boy! Why else do you think I feed you? I love this game!"

[`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) in browser scripts can be used to perform any HTTP request. It takes an options object where we can specify details like the headers and the HTTP Method we want to use (GET, POST, etc.). But if we omit this options object, `fetch()` is going to default to a classic GET request.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1/")
```

Now, `fetch()` doesn't *immediately* return the HTTP response object. That could take quite a while to receive, especially if your internet connection sucks eggs. So instead it returns a special object called a Promise. That's a subject we'll unpack in another lesson (or two), so I'm only going to gloss it. 

A Promise is actually a lot like what it sounds like: a promise from `fetch()` to eventually return the HTTP response. And when we do get the HTTP response back from `fetch()`, we use the method `.then()` to run a function of your choosing at that later time – a callback function, just like `.addEventListener()`.

```js
fetch("https://jsonplaceholder.typicode.com/posts/1/")
  .then(responseObject => console.log("This is the HTTP response object:", responseObject))
```

But the HTTP response body inside the response object is going to be in JSON (likely) – we've not yet hydrated it into a JavaScript Object. So the response object comes along with a method to perform the conversion ([`.json()`](https://developer.mozilla.org/en-US/docs/Web/API/Body/json)).

```js
fetch("https://jsonplaceholder.typicode.com/posts/1/")
  .then(responseObject => responseObject.json())
```

But since that conversion from JSON to a real Object, too, can take a while (particularly if the JSON body is large), that conversion method isn't going to immediately return the hydrated object: it's going to return another Promise to return the hydrated object. 

So we'll want to tack another call to `.then()` on the method chain, which will wait for the finished, fully-hydated object:

```js
fetch("https://jsonplaceholder.typicode.com/posts/1/")
  .then(responseObject => responseObject.json())
  .then(hydratedBody => console.log(hydratedBody))
```

Instead of my carefully-named versions above, you will often see the same thing, only with variable names like this:

```js
fetch("https://jsonplaceholder.typicode.com/posts/1/")
  .then(res => res.json())
  .then(data => console.log(data))
```

So any code that actually has to *wait* on the data to be returned from the server and hydrated, you'll want to trigger from inside that second callback function, like this sample code:

```js
// Sample JSON: `{ firstName: "Ted", lastName: "Crisp", id: 4345, biography: "Ted Crisp is a single father and the well-respected head of a research and development department at Veridian Dynamics, a soulless conglomerate." }`
const output = document.getElementById("output")

fetch("https://jsonplaceholder.typicode.com/posts/1/")
  .then(responseObject => responseObject.json())
  .then(hydratedBody => {

    output.dataset.characterId = hydratedBody.id
    output.querySelector("h1").textContent = `${hydratedBody.firstName} ${hydratedBody.lastName}`

    const biography = document.createTextNode(hydratedBody.biography)
    output.appendChild(biography)

  })
```

In synopsis:

1. `fetch()` will return a Promise (and you use `Promise.prototype.then()` on Promises).
2. That Promise will provide a Response object to `.then()`.
3. That Response object has a method called `.json()`.
4. `.json()` will return a Promise.
5. That Promise will provide the hydrated data to `.then()`.

### Further Resources
- https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
- https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data#Fetch
- https://developer.mozilla.org/en-US/docs/Web/API/Body/json
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then