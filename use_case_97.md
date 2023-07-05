# Use Case 97: Using Cloudflare Workers for A/B Testing

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
 * Get a cookie from a request
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

In this example, we are using the `fetch` event listener to handle incoming requests. We define two variants of our webpage, `VARIANT_A` and `VARIANT_B`. We then use a cookie to persist which variant a visitor sees. If the visitor does not have a cookie, we randomly assign them to one of the two variants. We then fetch the appropriate variant and set the cookie in the response headers.