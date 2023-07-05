# Use Case 59: Using Cloudflare Workers for A/B Testing

Cloudflare Workers can be used to implement A/B testing, which is a method of comparing two versions of a webpage or app against each other to determine which one performs better.

Here is an example of how you can use a Cloudflare Worker for A/B testing:

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
  const VARIANT_A = 'https://variant-a.example.com'
  const VARIANT_B = 'https://variant-b.example.com'
  const cookie = getCookie(request, NAME)
  const variants = [VARIANT_A, VARIANT_B]

  // Determine which variant to return
  let url
  if (cookie && variants.includes(cookie)) {
    url = cookie
  } else {
    url = variants[Math.floor(Math.random() * variants.length)]
  }

  // Fetch the variant
  let response = await fetch(url)

  // Persist the variant with a Set-Cookie header
  response = new Response(response.body, response)
  response.headers.append('Set-Cookie', `${NAME}=${url}; path=/`)

  return response
}

/**
 * Get a cookie from the request headers.
 * @param {Request} request
 * @param {string} name
 */
function getCookie(request, name) {
  let result = null
  let cookieString = request.headers.get('Cookie')
  if (cookieString) {
    let cookies = cookieString.split(';')
    cookies.forEach(cookie => {
      let cookieName = cookie.split('=')[0].trim()
      if (cookieName === name) {
        let cookieVal = cookie.split('=')[1]
        result = cookieVal
      }
    })
  }
  return result
}
```

In this example, the worker is listening for fetch events. When a request is received, it checks for a cookie that indicates which variant to serve. If no cookie is found, it randomly selects a variant and sets the cookie. The selected variant is then fetched and returned to the client, with the Set-Cookie header ensuring the client will receive the same variant on subsequent visits.