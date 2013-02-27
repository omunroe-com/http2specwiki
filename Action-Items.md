## Tokyo F2F

* Mark: re-ping the TLS WG for NPN status  **DONE**
* Eliot: write text for a registry of opaque strings to identify different things to negotiate for
* Martin: explain use case for weights in the DNS record
* Eliot: revise his RR proposal based upon discussion
* Roberto: revise draft and ship new example code for delta
* Herve: publish draft and ship example code for headerdiff **DONE**
* Roberto / Will / Hasan: assure that SPDY4 changes are raised as issues for WG to consider, or given to editors if purely editorial
* Mark: raise a new issue for handing Expect/100-continue  **DONE**
* Mark: raise a new issue for negotiation of trailers   **DONE**
* Mark: raise a new issue for indicating the end of a header block  **DONE**
* Mark: raise a new issue about assuring that routing data (e.g., :host, :path, :scheme) appears before headers  **DONE**
* Roberto: make detailed proposal for frame size w/ continuation bit **DONE**
* Will: make a detailed proposal for session-level flow control. Initial values to be 64k. **DONE**
* Eliot: amend his record to accommodate "initial options"
* Roberto: forward complete proposal from spdy4 for grouping and re-prioritisation  
* Hasan: write a proposal for pushpromise; moving associated-to from synstream to it.
* Will: double check that spdy's change to directionality for max streams has made it into http/2, or raise issue.  **DONE**
* Editors: flesh out security considerations for server push, or note in new issue
* Mike B: make proposal on list for security/privacy considerations of settings.
* Eliot + Martin: Start discussion of magic on list.  **DONE**
* Hasan: Send proposal about metrics to gather during testing to list.
* Mark: Continue discussion of testing / data gathering on list.
* Roberto: Allow control of state kept in the delta compressor **DONE**