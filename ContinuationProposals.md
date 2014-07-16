This page tracks the pros and cons of proposals to address CONTINUATION/header block size-related issues (#550 and #551).

Note that it's very possible we end up using more than one of these; however, they're listed separately so that their individual attributes can be explored.


## Drop CONTINUATION
Remove CONTINUATION completely from the specification, as per #548.

### Pros

* Headers are sent in one frame, which means that multiplexing can still happen (as long as max frame size limit is appropriate to connection). Addresses #550.
  * [roberto] I'm not sure I understand the above. I assume it means that there can be no HoL blocking. If so, then this is not true.
    * If there are 1000 bytes of payload, the HoL blocking exists for the amount of time it takes to send those 1000 bytes of payload.
    * The use (or lack of use) of CONTINUATION frames doesn't change this: One can achieve all of the same properties by having an implementation initiate the sending of HEADER (followed by CONTINUATION) when it knows it will have all of the headers to send, and will not stall.
* No more need to parse CONTINUATION frames to keep the header table in sync after too large a request
  * [roberto] the above is not true-- removing continuations does not change the requirement to parse any/all header data sent, since failing to interpret header data (even if just throwing it out) results in state desynch of the compressor. I believe this is conflating the idea of a max-compressed-header size with the idea of continuations, which are orthogonal: One can have continuations while also having a max-compressed-header size.

### Cons
* Imposes a hard limit on header blocks; if not sufficiently large, interop with HTTP/1 suffers.
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  * [willy] proxies already have to deal with too large headers (h1 and h2), so this does not add extra difficulties.
* Requires knowing size of outbound headers before sending
  * which implies all of the headers must be buffered before transmission
  * which disallows implementation choice to trade possible HoL blocking for reduction in state commitment.
  * and implies an unavoidable addition to latency proportional to header_size/bandwidth

### Requirements on implementations
* must buffer the entire compressed headers before emitting any data
* must not send compressed headers which exceed max header-frame size.
* must maintain max header-frame size, as per the next section.

## Limit header block size via a SETTING
Also proposed in #548, a recipient can send a setting that indicates how large a header block it's willing to receive; the most commonly discussed default is 16K and minimum is 256 octets, although both need more discussion.

### Pros
* Addresses #551; recipient can state the maximum header size permissible.

### Cons
* Gives information to attackers about how to maximally impact whilst staying within limits.
  * [willy] that's already the case in h1 where implementation limits are well-known
  * [roberto] that is a big IF. Many servers compute these dynamically and so these limits are not well known.
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  * [willy] same as above, already needed and handled anyway
* Does not reduce the machinery needed to deal with overly large headers:
  * One may still need to receive/send 431s.
  * One must still detect when the headers are too large and react.
  * One must still detect when the sender is zip-bombing and attempting to cause receivers to allocate large amounts of memory for uncompressed data. This is the larger DoS surface area.
* Requires the 'rewinding' of compression state (or accurate prediction of when this would happen, which would be magic) when, after compression, an opcode will not fit into the max frame size
  * This implies much more complexity for the compression implementation.

### Requirements:
* SETTINGS ID for max-compressed header size
* Implementations must buffer at least the last opcode
* Implementations must 'rewind' the compressor state, or make (magically) accurate predictions of compressed size.

### Notes

The 256 number is a back-of-the-envelope number PHK came up with, the thinking is the following:  It should always be possible to send "GET /robots.txt" and "Get /" and it should be possible to return a "deny all" and 3xx redirect respectively.  Mandating that all HTTP/2 implementations never limit the size lower than 256 ensures this very basic level of interop.  (But I'll appreciate if somebody else will redo the math using their own assumptions about FQDN lengths etc.)



## Interleave HEADER-bearing frames
Allow frames bearing headers to be interleaved without blocking other frames.

This point is only relevant for designs where headers can be fragmented across multiple frames.

### Pros
* Addresses #550; other streams can progress during transmission of a large header block.

### Cons
* Increases the DoS surface area multiplicatively by max_concurrency size:
  * Allows a DoS whereby an attacker opens a large number of streams with partial headers; the recipient often (but not always) needs to buffer each stream's headers, incurring a large cost in memory. 
    * [roberto] This is different from doing fragmentation without interleaving because in the fragmenting without interleaving case, the max DoS exposure is one incomplete set of headers, instead of max_concurrency sets of incomplete headers.
    * [gregw] I don't see this as substantially different from existing DoS exposure.  A request can be sent with content-length:1 and then that content never sent.  A server still needs to buffer the headers while waiting for the content and in many applications has a thread allocated to handle the request.
* Requires non-state modifying and non-dynamic-state referring compression.

### Requirements
* Implementations which wish to reliably interpret complete header sets must buffer incomplete headers.
  * If compressed header length is required, this requirement changes to *All* implementations must buffer incomplete headers before forwarding.
* No state-referring or state-modifying opcode may span non-contiguous frames
  * practically, this means no state-referring or state-modifying opcode may span any frames.
  * This ends up implying 'rewinding' of the compression state if/when an opcode ends up being larger than the max permissible frame size.


## Require CONTINUATION frames to follow "full" ones
When sending CONTINUATION, the previous HEADER-bearing frame MUST be "full". 

### Pros
* Makes death-by-a-thousand CONTINUATIONs (primarily a CPU DoS) harder, marginally helping #553 and #551.

### Cons
* Disallows flushing partial headers before forwarding a request, increasing buffering requirements in some scenarios.
* "Full" needs to be defined relative to how HPACK works, because a strict interpretation will force HPACK output to span frames, reintroducing the state-full-ness which removing the reference set was supposed to eliminate.
  * [roberto] The reference set thing is only a part of this; to remove statefulness one must remove statefulness, which includes any references to the header table, modifications to the header table and the reference set.
  * This implies that a remote party can help another party to affect a memory DoS but increasing the max allowable frame-size, since this places a buffering requirement on the transmitter which the transmitter cannot control.

### Requirements
* Emission of HEADERS data must wait until the headers frame is filled to the maximum frame size
* max-headers-frame-size must exist; It may be static (as it is today), or dynamic via SETTINGS.

## Require :-headers to be first
Require "routing" meta-headers to be serialised first (requires dropping reference set).

### Pros
* Makes policy decisions easier in *some* cases, partially addressing #551.
* Retains HTTP/1 semantics which a lot of implementations rely on, directly or indirectly.
* Potentially decreases latency at proxies for proxies where the ':' headers are sufficient to determine the routing of the request, as such proxies can preconnect, etc.

### Cons
* may depend on getting rid of the HPACK reference set ?
  * [phk] I don't think so, why would that be ?
    * [wt] because if any :-header is in the refset, it will automatically be sent after indexed headers
  * [roberto] It doesn't require getting rid of the reference set-- it would only require indexed opcodes to be sent to refer to these at the beginning of the frame. Sometimes it would require two opcodes, though this could be 'fixed' by treating ':' differently in the compressor, and with other approaches.

### Requirements
* must refer to the ':' headers first (duh...)
  * today this would imply HEADERS frames start with either one or two opcodes per ':' header, or implies a new opcode type or a different frame...

## Remove CONTINUATIONS, Fragment HEADERS using END_SEGMENT flag.

Remove the CONTINATION frame and allow HEADERS to be fragmented in the same way that DATA frames are:
* Remove the continuation frames. 
* Remove the END_HEADERS flag from the HEADERS frame.  Instead use the END_SEGMENT flag to indicate the end of a header block.
* END_STREAM flag semantics are the same as they are for DATA frames.
* Remove priority fields from the HEADERS frame, as these can be sent in a separate PRIORITY frame without concerns of fragmentation.
* Deduct the HEADER frame sizes from the flow control window sizes, but do not block header frames due to flow control. 
* If it is desired to declare a maximum header size (for 551), then add a SETTINGS_MAX_HEADER_SIZE expressed in uncompressed bytes
* Remove the header block fragment from the PUSH_PROMISE frame. Instead send the headers in a HEADERS+ frame sequence following the PUSH_PROMISE frame.
* Optionally rename HEADERS to META_DATA to avoid the sending the trailers in a headers frame confusion

### Pros
* Addresses 550 by allowing headers to be fragmented and interleaved.
* Addresses 551. Since limit is not related to framing, the max header size can be expressed as uncompressed header size.
* Addresses "uglyness" concerns about how to handle the flags on HEADERS/CONTINUATIONS. 
* Allows for infinite streaming headers


### Cons
* Presents same DoS attack surface as CONTINUATIONS
  * [roberto] Which refers to having to parse the continuation frame, and would be weaker than many of the other ways to attack the protocol: one-byte data payloads, settings sent often, PING sent often, etc. etc.
* Mixes the concept of the end of a segment and the concept of the end of the current set of headers
  * [gregw] Why are these concepts different? end-segment flag signals represents a semantic boundary within the content. Isn't that what end of headers is?
    * [roberto] Not necessarily-- non HTTP protocols may send messages which may consist of (metadata* data*)*. For such uses, using END_SEGMENT for END_HEADERS conflates end-of-message with end-of-headers, and would require signaling end-of-message with some other mechanism.

### Requirements
* If HEADERS fragments are interleaved, then HPACK must be changed to allow interleaving (see efficiency below).
* Intermediaries/servers/senders must place a different semantic understanding on empty header-sets and non-empty header-sets, since empty-header-sets are now indications of differnt things.
* If efficiency is a concern, a way of disabling the emission from the reference set must be utilized
  * could be removing the reference set
  * could be an opcode which changes how the reference set is emitted
  * could be turning off everything in the reference set (as one would/could do it today)

## Change the state machine to allow for INCOMPLETE_HEADER FRAME

Some of the reaction to CONTINUATION appears to be that handling of that frame differs in terms of flags.
Solve this by always having HEADERS have the flags which indicate END_STREAM, etc. This implies that HEADERS would be the last frame in the sequence of frames which represents a set of headers.

When an implementation is sending a set of headers, it may send a sequence of any number of non-empty INCOMPLETE_HEADERS_FRAMES, followed by a HEADERS frame. Flags on INCOMPLETE_HEADERS_FRAMES shall always be zero. The receipt of a HEADERS frame implies that the set of headers has been fully received once its payload has been interpreted.

### Pros
* Addresses uglyness complaints
* Allows for the removal of the END_HEADERS flag.

### Cons
* It is a change

### Requirements
* None if other protocol semantics remain unchanged.

## Summary of ideas (and their requirements) in this area:
  * header fragmentation vs non-fragmentation
  * limiting compressed header size for a single header set
    * requires a max setting
  * header interleaving
    * requires fragmentation (the inverse is not true-- fragmentation does not require interleaving)
  * eliminating stateful opcodes which cross frame boundaries
  * using END_SEGMENT instead of END_HEADERS to signal end of a set of headers
  * Send INCOMPLETE_HEADER_FRAME* HEADER_FRAME

