This document gives advice about how to configure lower-layer protocols and how to use HTTP/2's in-built mechanisms effectively. It is primarily focused on server configuration.

## TCP Configuration

HTTP/2 has been designed to use a single TCP connection, whereas current practice for HTTP/1 is to
use multiple connections to achieve parallelism (generally, between four and eight).

The change has a number of benefits. Using fewer TCP connections to load a Web page consumes less
server-side resources, and it also reduces the chance of a congestion event caused by a large
number of connections simultaneously starting (overcoming TCP Slow Start), and returns HTTP to
being a "fair" protocol. Using a single connection also enables better efficiency with header
compression.

However, using a single connection can also lead to unfavorable performance, as compared with
HTTP/1's use of multiple connections, primarily due to side effects of TCP congestion control.

In particular, a single HTTP/2 connection with an initcwnd of 3 can only have three unacknowledged
packets during the startup phase of the connection, whereas (for example) six HTTP/1 connections
would have as many as 18 packets outstanding. This places HTTP/2 at a significant disadvantage
compared to HTTP/1, but can be mitigated by adopting an initcwnd of 10 for HTTP/2 connections, as
outlined in RFC6928.

Similarly, a congestion event on a HTTP/2 connection can cause disproportionate havoc, as compared
to HTTP/1, in those cases where the event only affects a subset of open connections (such as random
packet loss). TBD: mitigation

Key recommendations:

**Servers should ensure initcwnd is at least 10** - initcwnd is a major bottleneck that impacts initial page load times. HTTP works around this by opening multiple connections simultaneously, essentially achieving n * initcwnd initial congestion window sizes.

**Turn off tcp_slow_start_after_idle** - Linux by default will have tcp_slow_start_after_idle set to 1. This drops cwnd back down to initcwnd and goes back to slow start after the connection has been “idle” (defined as the RTO). This kills persistent single connection performance and should be turned off.


## TLS Configuration

Beyond the typical performance and operational considerations of deploying TLS (RFC5246), a
concern specific to HTTP/2 is the TLS record size; because HTTP/2 is a multiplexed protocol, a
large record size can cause packet loss to affect a disproportionate number of streams, due to an
individual record not being available until it is complete.

Therefore, small record sizes are preferred for HTTP/2; if a record is sent within a single packet,
the chances of blocking are minimized. That said, records ought not be much smaller, since this
will increase processing overhead, and in some circumstances (e.g., non-interactive applications,
downloads), it may be reasonable to have larger record sizes.

Key recommendations:

**Use a small TLS record size** - ideally, small enough that a record fits completely in a single packet. Large TLS application records will span multiple packets. Since applications must process complete TLS application records, this may add latency.

**Use smaller, but complete, certificate chains** - The size of certificate chains can substantially affect connection startup performance due to increased serialization latency and using up precious bytes in the initcwnd, costing extra RTTs. Also, when the server does not present the full certificate chain, then clients have to fetch intermediate certificates, adding yet more roundtrips before the connection becomes usable by the application.

**Use wildcard certs** - HTTP/2 allows for connection sharing, which helps reduce the number of connections, which is beneficial for all the reasons already stated.


## Load Balancing and Failover 

It's common to use multiple servers to server a single HTTP origin, in order to provide a scalable
and reliable service. DNS is also commonly used to direct clients to the best (by some metric)
server available.

In HTTP/1, the transition from one server to another in these scenarios is often done between
connections; because HTTP/1 connections are generally short-lived, it's possible to load balance
clients as they re-connect.

HTTP/2, however, is designed to have fewer, longer-lived connections, and it's anticipated that
clients will be keeping them open much more aggressively. This provides fewer opportunities for
servers to shift traffic. If a server breaks connections pre-emtively in order to load balance or
failover, it can also have a greater negative effect, since more than one request can be "in
flight" simultaneously.

The new protocol accommodates these situations in a few ways, improving operability along the way.

Firstly, the GOAWAY frame allows servers to announce that they will not serve additional requests on
a connection, while still completing those that preceed the GOAWAY. This allows a connection to be
shut down in an orderly fashion, and its use is required in HTTP/2.

Additionally, the ALTSVC frame allows a server to redirect traffic to another location, without
changing the resource's URL. This can be used for load balancing (both local and global), as well
as controlled failover of services.


## Use of HPACK

TBD

## Use of Flow Control

TBD

## Use of Prioritisation

TBD

## Use of Server Push

TBD

## Application-Specific

**Use a single connection** - it’s better for HTTP/2 performance and for the internet to use as few connections as possible. For HTTP/2, this will result in better packing of data into packets, better header compression, less connection state, fewer handshakes, etc. It improves TCP behavior across the internet and reduces bufferbloat. Interacts better with NATs as well as it requires less state.

**Don’t shard hostnames** - This is a hack used by web apps to work around browser parallelism limits. For all the reasons that we suggest using a single connection, hostname sharding is suboptimal when you can use HTTP/2 instead. Furthermore, hostname sharding requires extra DNS queries and complicates web apps due to using multiple origins.

**Use server push instead of inlining** - This is a hack used by web apps to reduce RTTs. Inlining reduces the cacheability of web pages and may increase web page sizes due to base64 encoding. Instead, the server can just push the content.

**Use request prioritization** - Clients can advise the server on relative priorities for resources. Some basic heuristics which clients should all do generally is to make sure that html > js, css > *.

**Use reasonable frame sizes** - Although the spec allows for large frames, it is often desirable to use smaller frames, because this allows for better interleaving of frames from different streams.

**Don't use CSS sprites with external stylesheets** - Resources in external stylesheets are obviously only discovered after the external stylesheet has been downloaded, and only once the rule matches an element. The advantage they provide of reducing requests is unnecessary with HTTP/2 due to its multiplexing. Therefore, CSS sprites just make it slower.
