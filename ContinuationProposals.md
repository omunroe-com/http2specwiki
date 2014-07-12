This page tracks the pros and cons of proposals to address CONTINUATION/header block size-related issues (#550 and #551).

Note that it's very possible we end up using more than one of these; however, they're listed separately so that their individual attributes can be explored.


## Drop CONTINUATION
Remove CONTINUATION completely from the specification, as per #548.

### Pros

* Headers are sent in one frame, which means that multiplexing can still happen (as long as max frame size limit is appropriate to connection). Addresses #550.
* No more need to parse CONTINUATION frames to keep the header table in sync after too large a request

### Cons
* Imposes a hard limit on header blocks; if not sufficiently large, interop with HTTP/1 suffers.
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  [willy] proxies already have to deal with too large headers (h1 and h2), so this does not add extra difficulties.


## Limit header block size via a SETTING
Also proposed in #548, a recipient can send a setting that indicates how large a header block it's willing to receive; the most commonly discussed default is 16K and minimum is 256 octets, although both need more discussion.

### Pros
* Addresses #551; recipient can state the maximum header size permissible.

### Cons
* Gives information to attackers about how to maximally impact whilst staying within limits.
  [willy] that's already the case in h1 where implementation limits are well-known
* When headers are too big, error states may be problematic in proxies, existing HTTP APIs.
  [willy] same as above, already needed and handled anyway


### Notes

The 256 number is a back-of-the-envelope number PHK came up with, the thinking is the following:  It should always be possible to send "GET /robots.txt" and "Get /" and it should be possible to return a "deny all" and 3xx redirect respectively.  Mandating that all HTTP/2 implementations never limit the size lower than 256 ensures this very basic level of interop.  (But I'll appreciate if somebody else will redo the math using their own assumptions about FQDN lengths etc.)


## Interleave HEADER-bearing frames
Allow frames bearing headers to be interleaved without blocking other frames.

### Pros
* Addresses #550; other streams can progress during transmission of a large header block.

### Cons
* Allows a DoS whereby an attacker opens a large number of streams with partial headers; the recipient often (but not always) needs to buffer each stream's headers, incurring a large cost in memory.


## Require CONTINUATION frames to follow "full" ones
When sending CONTINUATION, the previous HEADER-bearing frame MUST be "full". 

### Pros
* Makes death-by-a-thousand CONTINUATIONs (primarily a CPU DoS) harder, marginally helping #553 and #551.

### Cons
* Disallows flushing partial headers before forwarding a request, increasing buffering requirements in some scenarios.

## Require :-headers to be first
Require "routing" meta-headers to be serialised first (requires dropping reference set).

### Pros
* Makes policy decisions easier in *some* cases, partially addressing #551.

### Cons
* ???
* may depend on getting rid of the HPACK reference set ?
