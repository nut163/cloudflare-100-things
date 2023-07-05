# Use Case 96: Using Cloudflare Workers for Content Optimization

Cloudflare Workers can be used to optimize the content of your website. This can be done by compressing images, minifying CSS and JavaScript, and more.

Here is an example of how you can use a Cloudflare Worker to minify JavaScript:

```javascript
addEventListener('fetch', event => {
  event.respondWith(minifyJS(event.request))
})

async function minifyJS(request) {
  const response = await fetch(request)

  if (response.headers.get('Content-Type') !== 'application/javascript') {
    return response
  }

  const text = await response.text()
  const minified = minify(text) // Assume minify is a function that minifies JS

  return new Response(minified, response)
}
```

In this example, the worker listens for fetch events. When a request is made, it checks if the content type of the response is JavaScript. If it is, it fetches the response, minifies the JavaScript, and returns the minified version.

This is just one of the many ways you can use Cloudflare Workers to optimize your website's content.