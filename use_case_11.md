# Use Case 11: Using Cloudflare Workers for A/B Testing

Cloudflare Workers can be used to implement A/B testing, which is a method of comparing two versions of a webpage or app against each other to determine which one performs better.

Here is a simple example of how you can use a Cloudflare Worker for A/B testing:

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

/**
 * Respond with one of two variants, controlled by a cookie
 * @param {Request} request
 */
async function handleRequest(request) {
  // Determine which group this request is in.
  const cookie = request.headers.get('cookie')
  const group = cookie && cookie.includes('__cf_A/B')
    ? 'B'
    : 'A'
  // Get the appropriate variant.
  const url = group === 'A'
    ? 'https://example.com/variantA'
    : 'https://example.com/variantB'
  const response = await fetch(url)
  // Set the cookie in the response.
  const newHeaders = new Headers(response.headers)
  newHeaders.append('Set-Cookie', `__cf_A/B=${group}; path=/`)
  return new Response(response.body, {
    status: response.status,
    statusText: response.statusText,
    headers: newHeaders
  })
}
```

In this example, we are using a cookie to determine which variant to serve to a user. If the cookie `__cf_A/B` is present, we serve variant B, otherwise we serve variant A. We then set the cookie in the response so that the user will continue to see the same variant.