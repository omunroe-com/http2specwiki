_This page records potential problems with the TLS requirements in [Section 9.2](http://http2.github.io/http2-spec/#TLSUsage), as per [issue 612](https://github.com/http2/http2-spec/issues/612)._ 


## Client Cipher Suite Ordering

Both clients and servers will want to still support peers that use HTTP/1 with cipher suites that are not allowed by HTTP/2, for backwards compatibility. However, this can lead to a situation where a cipher suite that does not conform to h2 requirements in 9.2 is negotiated in TLS, even though both peers support HTTP/2.

This can be addressed by the proposal in [pull 615](https://github.com/http2/http2-spec/pull/615):

> Clients that offer suites that are not valid for use in HTTP/2 SHOULD place all valid cipher suites before any invalid suites in the TLS ClientHello.

However, this requires clients' TLS implementation to have this capability. 

[RFC5246 says](http://tools.ietf.org/html/rfc5246#section-7.4.1.2):

> The cipher suite list, passed from the client to the server in the ClientHello message, contains the combinations of cryptographic algorithms supported by the client in order of the client's preference (favourite choice first).

Note that it is defined as a list, and client preference is highlighted as an important aspect of the protocol. 


## Server Information Availability

If a client offers what is known to the server to be a h2-compatible cipher suite, the server can choose h2 provided that ALPN also includes h2; if neither is true, the server will stay on h1.

However, if the TLS implementation in use by the server does not the decisions of cipher suite to use and protocol to use to be linked, it's possible that a cipher suite will be negotiated that is disallowed by HTTP/2.

This effectively prevents conformant deployment of h2 on such platforms, until they are modified or alternate implementations of TLS are used. 

Non-conformant deployment is still possible if they ignore the requirement to raise INADEQUATE_SECURITY when the cipher suite isn't sufficient; however, the client still may raise INADEQUATE_SECURITY (e.g., if it had only included non-conformant cipher suites for backwards compatibility). 

As such, the issue here is a conflict between our desire to improve security in the new protocol and our desire for immediate, wide adoption.

## Cipher Suite Update

If a client offers a new cipher suite that it believes to be conformant to h2 requirements, but the server does not yet recognise it as conformant, it will not be used; instead, they will fall back to another cipher suite (ultimately, the one required for interop if no other matches).

Thus, 9.2 makes it marginally harder to introduce new cipher suites, because both sides have to recognise them as conformant to h2 requirements.


