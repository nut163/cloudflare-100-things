# Use Case 35: Using Cloudflare Workers for A/B Testing

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
  const NAME = 'experiment-0'
  const VARIANT_A = 'https://variant-a.example.com'
  const VARIANT_B = 'https://variant-b.example.com'
  const cookie = request.headers.get('cookie')
  if (cookie && cookie.includes(`${NAME}=variant-a`)) {
    return fetch(VARIANT_A)
  } else if (cookie && cookie.includes(`${NAME}=variant-b`)) {
    return fetch(VARIANT_B)
  } else {
    // No cookie, do the experiment
    const group = Math.random() < 0.5 ? 'variant-a' : 'variant-b'
    const response = await fetch(group === 'variant-a' ? VARIANT_A : VARIANT_B)
    // Set a new cookie for future requests
    response.headers.append('Set-Cookie', `${NAME}=${group}; path=/`)
    return response
  }
}
```

In this example, the worker checks for a cookie that indicates whether this request belongs to variant-a or variant-b. If no cookie is found, it makes a random decision on which group to assign the request to, fetches the appropriate variant, and sets a cookie to remember the group for future requests.