# Use Case 31: Implementing a Custom 404 Page with Cloudflare Workers 🚀

In this use case, we will explore how to use Cloudflare Workers to implement a custom 404 page. This can be useful when you want to provide a more user-friendly or branded error page when a resource is not found.

## Explanation 📚

Cloudflare Workers can intercept HTTP requests and modify the response. In this case, we can check if the response status is 404, and if so, return a custom error page.

## Example 🧪

Here's a simple example of how you can implement this:

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  const response = await fetch(request)

  if (response.status === 404) {
    return new Response('Page not found 😞', {status: 404})
  }

  return response
}
```

In this example, if the original response status is 404, we return a new Response with a custom message and the same 404 status.

## Pros and Cons 🏁

### Pros

1. **User Experience**: A custom 404 page can provide a better user experience than a generic error page.
2. **Branding**: You can use your brand's style and voice in the custom 404 page.

### Cons

1. **Complexity**: This adds a bit of complexity to your Cloudflare Worker script.
2. **Cost**: Depending on the number of 404 errors, this could increase the cost of your Cloudflare Workers usage.

## Joke of the day 😂

Why don't programmers like nature? It has too many bugs! 🐞

[Back to Table of Contents](table_of_contents.md)