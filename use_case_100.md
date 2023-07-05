# Use Case 100: Using Cloudflare Workers for Load Balancing

Cloudflare Workers can be used to implement a simple load balancer. This can be useful when you have multiple origins and you want to distribute the traffic among them.

Here is a simple example:

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

const ORIGINS = [
  'https://origin1.example.com',
  'https://origin2.example.com',
  'https://origin3.example.com'
]

async function handleRequest(request) {
  const url = new URL(request.url)
  const origin = ORIGINS[Math.floor(Math.random() * ORIGINS.length)]
  url.hostname = new URL(origin).hostname
  const newRequest = new Request(url, request)
  return fetch(newRequest)
}
```

In this example, we have an array of origins. When a request comes in, we randomly select one of the origins, change the hostname of the request URL to the selected origin, and then forward the request to that origin.

This is a very basic example and does not take into account the health of the origins or other factors that you might want to consider when implementing a load balancer. However, it demonstrates the flexibility of Cloudflare Workers and how they can be used to modify requests and responses in powerful ways.