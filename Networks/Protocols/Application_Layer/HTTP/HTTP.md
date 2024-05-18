---
tags:
  - Networks
  - Protocols
  - Application_Layer
---
# Core principles
## Status codes
- `[100, 199]`: Informative response
- `[200, 299]`: Successful responses (example: `200 OK`)
- `[300, 399]`: Redirects (example: `301 Moved permanently`)
- `[400, 499]`: Client errors (example: `400 Bad request` or `404 Not found`)
- `[500, 599]`: Server errors (example: `500 Internal server error`)
## REST
_REST stands for REpresentational State Transfer_
It is not an [[Internet#API|API]] itself. It is a **software architecture** designed so as to ease the application maintenance.
The fundamentals of REST are:
- **Resources**: each resource (image, JSON object, audio, data...) has got its own URL (e.g. `/movies/123` is the JSON object that represents the movie number 123)
- **HTTP methods**: each method has got its own operation (GET, PUT, POST, DELETE...)
- **Representation**: each resource can be represented in different ways; for example, the movie 123 can be represented as JSON plain-text or can be rendered in client side as an image, topology or whatever. The client decides how to represent them
- **Stateless**: the server side doesn't store client information, but the client must send all the information in the **URL**
- **Client and server evolve separately**
The REST API can fulfill (or not) [[#Idempotency]] (it is not mandatory).
### Idempotency
Idempotency is a property where making the same API request multiple times has the same effect as making it just once, as it does not depend of the number of operations.
- If our API implements POST operations, it won't be idempotent, as each POST will create a new resource
- The PUT operation is idempotent, as it just updates an existing object
The idempotent operations are: POST, PATCH.
The non-idempotent operations are the rest.
## SOAP

# HTTPS
It is the combination of HTTP Application Layer Protocol with [[SSL-TLS]] Transport Layer Protocol.
A server that uses HTTP**S** contains inside a [[SSL-TLS]] [[Cybersecurity#Certificates|certificate file]] so that server can be trusted to others. That SSL-TLS certificate file has been signed by some [[Cybersecurity#Certificate Authorities (CA)|Certificate Autority]], so client must also trust the Certificate Authority when communicating to server.
The 
