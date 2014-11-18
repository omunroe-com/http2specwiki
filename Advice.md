Things we have learned and common mistakes - todo

* The spec notes that [stream ids must be increasing](http://http2.github.io/http2-spec/#StreamIdentifiers), but this can be easy to accidentally violate. If after frame generation, you insert the frame into a priority queue, then it's possible for a higher priority frame to move ahead. Therefore, stream ids must be generated/committed later in processing in order to ensure correct stream id ordering.

* Implementations need to make sure they are always pumping data from the underlying transport to the HTTP/2 layer, otherwise there can be hangs. This is because a sender may be blocked on a zero flow control window. If it doesn't keep reading data from the socket, it may not pick up the WINDOW_UPDATE frame that reopens the flow control window.

* Some client implementations deal very poorly if a server responds to both [ALPN](https://tools.ietf.org/html/rfc7301) and [NPN](https://technotes.googlecode.com/git/nextprotoneg.html).  The behavior here is ambiguous and not defined, as NPN was never standardized and as such this is not explicitly prohibited by the ALPN specification.  When a server responds in the ServerHello and negotiates a protocol via ALPN, it *must not* also send an NPN negotiation with a list of protocols even if the client sends both ALPN and NPN extensions in its ClientHello.

