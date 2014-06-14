This wiki tracks known implementations of HTTP/2. If you have questions or issues updating it, please contact [Mark Nottingham](mailto:mnot@mnot.net).

implementation | language | role(s) | negotiation(s) | draft support
--- | --- | --- | --- | ---
[nghttp2](https://nghttp2.org) | C | client, server, intermediary | ALPN, NPN, Upgrade, direct | **draft-12**
[http2-katana](https://github.com/MSOpenTech/http2-katana) | C# | server, test client | ALPN, Upgrade | **draft-12**
[node-http2](https://github.com/molnarg/node-http2) | NodeJS | server, client | ALPN, NPN, direct | **draft-12**
[Mozilla](https://wiki.mozilla.org/Networking/http2) | C++ | client | ALPN, NPN | **draft-12**
[http2-perl](https://github.com/sludin/http2-perl) | Perl | client, server | NPN | draft-04
[iij-http2](https://github.com/shigeki/interop-iij-http2) | NodeJS | client, server| ALPN, NPN | **draft-12**
[Akamai Ghost](Akamaighost) | C++ | intermediary | NPN | **draft-12**
[Chromium](https://sites.google.com/a/chromium.org/dev/spdy/http2) | C++ | client | ALPN, NPN | **draft-12**
[Hasan's GFE](Hasansgfe) | C++ | intermediary | ALPN, NPN | draft-04
[Twitter](https://twitter.com/) | Java | server, client | NPN | **draft-12**
[Wireshark](https://bugs.wireshark.org/bugzilla/show_bug.cgi?id=9042) | C | other | ALPN, NPN, Direct, Upgrade | **draft-12**
[Ericsson MSP](EricssonMPS) | | proxy | NPN, Upgrade, direct | draft-06
[http2-go](https://github.com/Jxck/http2) | golang | client, server | npn | draft-10
[OkHttp](https://github.com/square/okhttp) | Android, Java | mock server, client | ALPN, NPN | **draft-12**
[mruby-http2](https://github.com/matsumoto-r/mruby-http2) | C/mruby | client, server | ALPN, NPN, Direct | **draft-12**
[http-2](https://github.com/igrigorik/http-2) | Ruby | server, client | NPN, direct | draft-06
[hyper](https://github.com/lukasa/hyper) | Python | client | NPN | draft-09
[curl and libcurl](https://curl.haxx.se/) | C | client | ALPN, NPN, Upgrade | **draft-12**
[cl-http2-protocol](https://github.com/akamai/cl-http2-protocol) | Common Lisp | client, server | NPN, direct | draft-12
[Netty](http://netty.io/) | Java | client, server | ALPN, NPN | **draft-12**
[Jetty](http://git.eclipse.org/c/jetty/org.eclipse.jetty.project.git/tree/?h=jetty-http2) | Java | client, server | NPN, ALPN | **draft-12**