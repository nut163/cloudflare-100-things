# Use Case 84

In this use case, we will demonstrate how to use Cloudflare Workers to implement a simple A/B testing.

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

/**
 * Respond with one of two variants, stored in `VARIANTS`,
 * and set a cookie to persist a visitor's variant.
 * @param {Request} request
 */
async function handleRequest(request) {
  const NAME = 'experiment-0'
  const VARIANT_A = 'https://example.com/variant-a'
  const VARIANT_B = 'https://example.com/variant-b'
  const VARIANTS = [VARIANT_A, VARIANT_B]

  // Determine which group this request is in.
  const cookie = request.headers.get('cookie')
  if (cookie && cookie.includes(`${NAME}=a`)) {
    return fetch(VARIANT_A)
  } else if (cookie && cookie.includes(`${NAME}=b`)) {
    return fetch(VARIANT_B)
  } else {
    // This is a new client, decide a group and set the cookie.
    let group = Math.random() < 0.5 ? 'a' : 'b' // 50/50 split
    let response = await fetch(VARIANTS[group === 'a' ? 0 : 1])
    response = new Response(response.body, response)
    response.headers.append('Set-Cookie', `${NAME}=${group}; path=/`)
    return response
  }
}
```

In this example, we are using Cloudflare Workers to serve one of two variants of a webpage to a user and set a cookie to remember the user's variant. This is a simple implementation of A/B testing, where we can test two versions of a webpage to see which one performs better.