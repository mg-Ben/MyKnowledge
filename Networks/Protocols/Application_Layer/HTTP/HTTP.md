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
# HTTPS
It is the combination of HTTP Application Layer Protocol with [[SSL-TLS]] Transport Layer Protocol.
A server that uses HTTP**S** contains inside a [[SSL-TLS]] [[Cybersecurity#Certificates|certificate file]] so that server can be trusted to others. That SSL-TLS certificate file has been signed by some [[Cybersecurity#Certificate Authorities (CA)|Certificate Autority]], so client must also trust the Certificate Authority when communicating to server.
The 
