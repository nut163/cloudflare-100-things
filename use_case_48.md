# Use Case 48: Using Cloudflare Workers for A/B Testing

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
  const NAME = 'experiment-1'
  const VARIANT_A = 'https://variant-a.example.com'
  const VARIANT_B = 'https://variant-b.example.com'
  const cookie = getCookie(request, NAME)
  const url = cookie ? (cookie === 'a' ? VARIANT_A : VARIANT_B)
                     : Math.random() < 0.5 ? VARIANT_A : VARIANT_B
  const response = await fetch(url)
  const newResponse = new Response(response.body, response)
  newResponse.headers.append('Set-Cookie', `${NAME}=${url === VARIANT_A ? 'a' : 'b'}; path=/`)
  return newResponse
}

/**
 * Get a cookie from the request headers
 * @param {Request} request
 * @param {string} name
 */
function getCookie(request, name) {
  let result = null
  const cookieString = request.headers.get('Cookie')
  if (cookieString) {
    const cookies = cookieString.split(';')
    cookies.forEach(cookie => {
      const cookieName = cookie.split('=')[0].trim()
      if (cookieName === name) {
        const cookieVal = cookie.split('=')[1]
        result = cookieVal
      }
    })
  }
  return result
}
```

In this example, the worker is set up to respond with one of two variants of a webpage and set a cookie to persist a visitor's variant. The variant is chosen randomly for first-time visitors, and returning visitors will see the same variant they saw on their first visit.