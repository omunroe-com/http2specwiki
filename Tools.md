# HTTP/2 Tools

**This is a listing of tools for analysing, debugging and visualising HTTP/2. See also the [Implementations listing](Implementations).**


* [Curl](http://curl.haxx.se) supports HTTP/2<sup>1</sup> as of 7.43.0. See its [documentation](http://curl.haxx.se/docs/http2.html) for details (including prerequisites).
* [h2i](https://github.com/bradfitz/http2/tree/master/h2i) is a command-line interactive client that lets you send H2 frames, translate H1 to H2, and generally figure out how the protocol works.
* [is-http2](https://github.com/stefanjudis/is-http2-cli) lets you quickly find out if a host supports H2 from the command line.
* [Wireshark](https://wireshark.org/) has a [HTTP/2 decoder](https://wiki.wireshark.org/HTTP2)<sup>2</sup>.

---

<sup>1</sup> Curl is strictly an [implementation](Implementations), but it's listed here because many people use it as a tool.

<sup>2</sup> Note that to use it on TLS-protected connections, you'll need to do [NSS Keylogging](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Key_Log_Format). See also the [Wireshark SSL/TLS docs](https://wiki.wireshark.org/SSL).




