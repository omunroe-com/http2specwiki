

## TCP Connections

In HTTP/1, a primary DoS vector is consuming available TCP connections on the server. Partially, this is because browsers typically uses many connections (between four and eight) with HTTP/1, making it a more scarce resource.

This is less of a concern in HTTP/2, because typically a client will use a single connection, thereby making many more connections available. Abusive clients should "stand out" more, because they will open many connections, whereas legitimate clients will usually only open one (even if they are proxies, although it remains to be seen how proxies will use connections).

HTTP/2 also gives servers more control over the lifetime of a connection; if resources become scarce (or if the server determines that the client *might* be abusive), the connection can be closed down in an orderly fashion (using RESET_STREAM and GOAWAY) without risking having legitimate requests interrupted (assuming the client will retry failed these requests automatically).

On the other hand, HTTP/2 gives the best performance for longer-lived connections, which means that servers will want to keep connections around for as long as possible -- making management of the connection pool critical for good performance as well as DoS protection.

## Memory

Because HTTP/2 uses stateful header compression (in HPACK), there is a per-connection memory overhead. The server's exposure to memory overhead can be tuned using appropriate SETTINGS, in response to a variety of factors.

Furthermore, HTTP/2 requires a certain amount of buffering of messages, whether they are to be forwarded to another node or consumed by an application. Again, it is possible to manage the size of these buffers using flow control. In extreme cases, the amount of buffering can also be affected by selected maximum frame size (which again can be advertised using a setting).

## CPU

HTTP/2's CPU overhead is favourable, as compared to HTTP/1 as well as SPDY. This is because it is a binary protocol, and also because HPACK has been shown to be less CPU-hungry than GZIP.

## Network

Because HTTP/2 includes header compression, it is possible to use the protocol to amplify the payload carried. However, it is possible to advertise limits using a setting, and to enforce them using normal HTTP mechanisms (e.g., the 431 response status code).

## Multiplexing

Because a HTTP header block cannot be interrupted by other frame types, it is possible for attack traffic to block progress on other streams using the same connection. For example, if a proxy is muxing together traffic from a variety of clients onto a single connection to the origin server, and forwards a partial header set from an attacking client, that connection cannot be used for other clients' traffic until the header block is complete.

Intermediaries can mitigate this attack in two ways:

1. Buffer entire header blocks before forwarding them. This requires more memory, although the intermediary can terminate header blocks that get too large.

2. Use continuations to stream out partial header blocks; if they stall or get too large, the intermediary can reset the stream with an appropriate error code. 

Note that this attack is bidirectional; a malicious (or misbehaving, or poorly-connected) server could also do this on the response path.