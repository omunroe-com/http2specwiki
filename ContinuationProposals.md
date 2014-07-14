This page tracks the pros and cons of proposals to address CONTINUATION/header block size-related issues (#550 and #551).

Note that it's very possible we end up using more than one of these; however, they're listed separately so that their individual attributes can be explored.


## Drop CONTINUATION
Remove CONTINUATION completely from the specification, as per #548.

### Pros

* Headers are sent in one frame, which means that multiplexing can still happen (as long as max frame size limit is appropriate to connection). Addresses #550.
* No more need to parse CONTINUATION frames to keep the header table in sync after too large a request
  * [roberto] the above is not true-- removing continuations does not change the requirement to parse any/all header data sent, since failing to interpret header data (even if just throwing it out) results in state desynch.

### Cons
* Imposes a hard limit on header blocks; if not sufficiently large, interop with HTTP/1 suffers.
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  [willy] proxies already have to deal with too large headers (h1 and h2), so this does not add extra difficulties.
* Requires knowing size of outbound headers before sending
  * which implies all of the headers must be buffered before transmission
  * which disallows implementation choice to trade possible HoL blocking for reduction in state commitment.
  * and implies an unavoidable addition to latency proportional to header_size/bandwidth


## Limit header block size via a SETTING
Also proposed in #548, a recipient can send a setting that indicates how large a header block it's willing to receive; the most commonly discussed default is 16K and minimum is 256 octets, although both need more discussion.

### Pros
* Addresses #551; recipient can state the maximum header size permissible.

### Cons
* Gives information to attackers about how to maximally impact whilst staying within limits.
  [willy] that's already the case in h1 where implementation limits are well-known
  [roberto] that is a big IF. Many servers compute these dynamically.
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  [willy] same as above, already needed and handled anyway


### Notes

The 256 number is a back-of-the-envelope number PHK came up with, the thinking is the following:  It should always be possible to send "GET /robots.txt" and "Get /" and it should be possible to return a "deny all" and 3xx redirect respectively.  Mandating that all HTTP/2 implementations never limit the size lower than 256 ensures this very basic level of interop.  (But I'll appreciate if somebody else will redo the math using their own assumptions about FQDN lengths etc.)


## Interleave HEADER-bearing frames
Allow frames bearing headers to be interleaved without blocking other frames.

This point is only relevant for designs where headers can be fragmented across multiple frames.

### Pros
* Addresses #550; other streams can progress during transmission of a large header block.

### Cons
* Increases the DoS surface area multiplicatively by max_concurrency size:
  * Allows a DoS whereby an attacker opens a large number of streams with partial headers; the recipient often (but not always) needs to buffer each stream's headers, incurring a large cost in memory. [Can it be explained why this is any different to CONTINUATIONs without interleaving?]
* Requires non-state modifying and non-dynamic-state referring compression.
 


## Require CONTINUATION frames to follow "full" ones
When sending CONTINUATION, the previous HEADER-bearing frame MUST be "full". 

### Pros
* Makes death-by-a-thousand CONTINUATIONs (primarily a CPU DoS) harder, marginally helping #553 and #551.

### Cons
* Disallows flushing partial headers before forwarding a request, increasing buffering requirements in some scenarios.
* "Full" needs to be defined relative to how HPACK works, because a strict interpretation will force HPACK output to span frames, reintroducing the state-full-ness which removing the reference set was supposed to eliminate.
  * [roberto] The reference set thing is orthogonal; to remove statefulness one must remove statefulness, which includes the reference set, references to the header table, and modifications to the header table.

## Require :-headers to be first
Require "routing" meta-headers to be serialised first (requires dropping reference set).

### Pros
* Makes policy decisions easier in *some* cases, partially addressing #551.
* Retains HTTP/1 semantics which a lot of implementations rely on, directly or indirectly.

### Cons
* ???
* may depend on getting rid of the HPACK reference set ?  (I don't think so, why would that be ? /phk) [wt: because if any :-header is in the refset, it will automatically be sent after indexed headers /wt]
  * [roberto] It doesn't require getting rid of the reference set-- it would only require indexed opcodes to be sent to refer to these at the beginning of the frame. Sometimes it would require two opcodes, though this could be 'fixed' by treating ':' differently in the compressor, and with other approaches.


## Fragment HEADERS in the same way as DATA frames

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
* Addresses the "uglyness" and complexity concerns raised by not represented as issues. 
* Allows for infinite streaming headers
* can work with ":- first" proposal

### Cons
* Depends on removing HPACK reference set to allow interleaving of HEADERS frames
  * [roberto] interleaving and fragmentation are related, since interleaving requires fragmentation, however they are orthogonal-- one can have fragmentation without interleaving.
* Presents same DoS attack surface as CONTINUATIONS
  * [roberto] Which refers to having to parse the continuation frame, and would be weaker than many of the other ways to attack the protocol: one-byte data payloads, settings sent often, PING sent often, etc. etc.
