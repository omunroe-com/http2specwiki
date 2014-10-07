_This page records potential problems with the TLS requirements in [Section 9.2](http://http2.github.io/http2-spec/#TLSUsage), as per [issue 612](https://github.com/http2/http2-spec/issues/612)._ 


## Client Cipher Suite Ordering

For backwards compatibility, both clients and servers will want to support peers that use HTTP/1 with cipher suites that are not allowed by HTTP/2. However, this can lead to a situation where a cipher suite that does not conform to h2 requirements in 9.2 is negotiated in TLS, even though both peers support HTTP/2.

This can be addressed by the proposal in [pull 615](https://github.com/http2/http2-spec/pull/615):

> Clients that offer suites that are not valid for use in HTTP/2 SHOULD place all valid cipher suites before any invalid suites in the TLS ClientHello.

However, this requires clients' TLS implementation to have such a capability. 

[RFC5246 says](http://tools.ietf.org/html/rfc5246#section-7.4.1.2):

> The cipher suite list, passed from the client to the server in the ClientHello message, contains the combinations of cryptographic algorithms supported by the client in order of the client's preference (favourite choice first).

Note that it is defined as a list, and client preference is highlighted as an important aspect of the protocol.  Note also that as long as the h2 mandatory cipher suite is placed above all non-compliant suites, a server that selects the first available suite from the client's list will select a valid suite.

Therefore, the issue here is the tradeoff between increased security offered by 9.2 and the lack of support for clients who are unable to order their cipher suites, despite the definition of the offered suites as an ordered list in TLS.


## Server Cipher Suite and Protocol Selection

If a client offers what is known to the server to be a h2-compatible cipher suite, the server can choose h2 provided that ALPN also includes h2; if neither is true, the server will stay on h1.

However, if the TLS implementation in use by the server does not allow the decisions of cipher suite to use and protocol to use to be linked, it's possible that a cipher suite will be negotiated that is disallowed by HTTP/2.

This effectively prevents conformant deployment of h2 on such platforms, until they are modified or alternate implementations of TLS are used.

Non-conformant deployment is still possible if servers ignore the requirement.  However, a conformant client will issue an INADEQUATE_SECURITY connection error if it had only included non-conformant cipher suites for backwards compatibility.

As such, the issue here is a conflict between our desire to improve security in the new protocol and our desire for immediate, wide adoption.


## Cipher Suite Update

If a client offers a new cipher suite that it believes to be conformant to h2 requirements, but the server does not yet recognise it as conformant, it will not be used; instead, they will fall back to another cipher suite (ultimately, the one required for interop if no other matches).

TLS 1.2 only defines three values for SecurityParameters.cipher_type; GenericStreamCipher, GenericBlockCipher and GenericAEADCipher. A suite that offers forward secrecy can be identified through its key exchange algorithm, of which valid choices are DHE (ephemeral Diffie-Hellman) or ECDHE (elliptic curve DH), though TLS offers a number of other key exchange options.  Based on this, it should be possible to recognise a conformant cipher suite without recoding the server, mitigating this issue.

Furthermore, [pull 615](https://github.com/http2/http2-spec/pull/615) limits the scope of applicability of 9.2's requirements to TLS 1.2, further mitigating the issue.