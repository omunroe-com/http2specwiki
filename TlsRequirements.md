_This page records potential problems with the TLS requirements in [Section 9.2](http://http2.github.io/http2-spec/#TLSUsage), as per [issue 612](https://github.com/http2/http2-spec/issues/612)._ 

## Server Information Availability

If a client offers what is known to the server to be a h2-compatible cipher suite, the server can choose h2 provided that ALPN also includes h2; if neither is true, the server will stay on h1.

However, if the server does not provide all of this information (cipher suite and ALPN offers) to the HTTP stack at the time this decision is made, the only safe choice is to fall back to h1.

This effectively prevents conformant deployment of h2 on platforms where such TLS information isn't available, until they are modified or alternate implementations of TLS are used. 

Non-conformant deployment is still possible if they ignore the requirement to raise INADEQUATE_SECURITY when the cipher suite isn't sufficient; however, it's likely that the client (being conformant) will do so, closing the connection.