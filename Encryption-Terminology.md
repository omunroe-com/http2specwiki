Note that these terms apply **only** to TLS that uses (possible) server authentication. In specific, the terms do not work in the very few cases where TLS client authentication is required for a TLS session to be set up, and the terms definitely don't work for encryption protocols with equal peers like IPsec.

In these definitions, "cryptographic material" is used instead of "encryption key" because the process produces other important outputs.

**Server-authenticated encryption** -- Encryption that follows a process that ends with shared cryptographic material. During the agreement process, the client authenticated the server's identity in some way (such as through a trusted third party or pre-existing cryptographic knowledge). Active attackers who inserted themselves in the process would cause the process to fail and return no shared cryptographic material. 

**Better-than-nothing encryption** -- Encryption that follows a process that ends with shared cryptographic material. During the agreement process, the client did not authenticate the server's identity. Active attackers may have inserted themselves in the process could know the cryptographic material and thus be able to decrypt subsequent traffic that uses the derived keys. 

**Best-effort encryption** -- A process to establish cryptographic material. In this process, the client attempts to authenticate the server's identity but, if that is not possible, continues anyway. The result of best-effort encryption is either server-authenticated encryption or better-than-nothing encryption.

**Opportunistic encryption** -- A process that causes best-effort encryption to happen without an initiator attempting to start encryption. For example, the STARTTLS mechanism in SMTP provides opportunistic encryption.

Current HTTP using TLS is never opportunistic because HTTP over TLS always starts on port 443, not port 80. There are now proposals to create opportunistic encryption in HTTP/2 by either redirecting an HTTP/2 connection to port 443 or by initiating best-effort encryption in HTTP/2 headers.
