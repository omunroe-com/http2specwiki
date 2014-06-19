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
 * HTTP/2 servers SHOULD adopt an initcwnd of 10, as per RFC6928.


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
 * HTTP/2 servers SHOULD use a small TLS record size; ideally, small enough that a record fits completely in a single packet.


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
