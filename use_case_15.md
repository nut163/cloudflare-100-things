# Use Case 15: Using Cloudflare Workers for A/B Testing

Cloudflare Workers can be used to implement A/B testing, which is a method of comparing two versions of a webpage or app against each other to determine which one performs better.

Here is an example of how you can use a Cloudflare Worker for A/B testing:

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

/**
 * Respond with one of two variants, stored in `VARIANTS`,
 * and set a cookie to persist a visitor's variant
 * @param {Request} request
 */
async function handleRequest(request) {
  const NAME = 'experiment-0'
  const VARIANT_A = 'https://variant-a.example.com'
  const VARIANT_B = 'https://variant-b.example.com'
  const cookie = request.headers.get('cookie')
  if (cookie && cookie.includes(`${NAME}=a`)) {
    return fetch(VARIANT_A)
  } else if (cookie && cookie.includes(`${NAME}=b`)) {
    return fetch(VARIANT_B)
  } else {
    // User hasn't visited before or didn't have a variant cookie
    let group = Math.random() < 0.5 ? 'a' : 'b' // 50/50 split
    let response = await fetch(group === 'a' ? VARIANT_A : VARIANT_B)
    // Set a new cookie for future requests
    response = new Response(response.body, response)
    response.headers.append('Set-Cookie', `${NAME}=${group}; path=/`)
    return response
  }
}
```

In this example, the worker is set up to serve one of two variants of a webpage, and uses a cookie to remember which variant a visitor has seen. When a request comes in, the worker checks for a cookie that's been set in a previous visit. If one is found, the worker serves the same variant as before. If no cookie is found, the worker chooses a variant at random, serves it, and sets a cookie so it'll remember the variant for future visits.