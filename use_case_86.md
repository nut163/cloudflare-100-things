# Use Case 86: Using Cloudflare Workers for A/B Testing

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
  const cookie = getCookie(request, NAME)
  const variants = [VARIANT_A, VARIANT_B]

  // Determine which group this request is in.
  const group = cookie ? cookie.value : Math.random() < 0.5 ? 'A' : 'B'
  const url = variants[group === 'A' ? 0 : 1]

  // Make the request
  let response = await fetch(url)

  // Persist the group
  response = new Response(response.body, response)
  response.headers.append('Set-Cookie', `${NAME}=${group}; path=/`)

  return response
}

/**
 * getCookie looks for the cookie in the request headers
 * @param {Request} request incoming Request
 * @param {string} name of the cookie to get
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

In this example, we are using the `fetch` event listener to handle incoming requests. We determine which variant to serve based on a cookie. If the cookie doesn't exist, we randomly assign the user to a group and set the cookie. We then fetch the appropriate variant and return it as the response.