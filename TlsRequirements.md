_This page records potential problems with the TLS requirements in Section 9.2.2, as per http2/http2-spec#612._ 

## Server Information Availability

If a client offers what is known to the server to be a h2-compatible cipher suite, the server can choose h2 provided that ALPN also includes h2; if neither is true, the server will stay on h1.

However, if the server does not provide all of this information (cipher suite and ALPN offers) to the HTTP stack at the time this decision is made, the only safe choice is to fall back to h1.

This effectively prevents deployment of h2 on platforms where such TLS information isn't available, until they are modified or alternate implementations of TLS are used.