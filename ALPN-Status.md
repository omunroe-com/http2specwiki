This page tracks implementation status of [ALPN](http://tools.ietf.org/html/rfc7301).

Language | Library | Status | Notes
--- | --- | --- | --- 
C | [OpenSSL](http://www.openssl.org) | [released](http://git.openssl.org/gitweb/?p=openssl.git;a=blob;f=NEWS;h=fc466ae3cc1ee38b566516eafe913e32409798a4;hb=HEAD) | as of 1.0.2
C | [GnuTLS](http://www.gnutls.org/) | [released](http://gnutls.org/manual/html_node/Application-Layer-Protocol-Negotiation-_0028ALPN_0029.html) | as of 3.2.0 (stable-next)
C | [NSS](https://developer.mozilla.org/en/docs/NSS) | [released](https://developer.mozilla.org/en-US/docs/NSS/NSS_3.15.5_release_notes#New_Functionality) | client and server support
C | [PolarSSL](https://polarssl.org) | [released](https://polarssl.org/tech-updates/releases/polarssl-1.3.6-released) | since 1.3.6 
Python | [native](http://docs.python.org/3/library/ssl.html) | [in 3.5 and 2.7.10](http://bugs.python.org/issue20188) | OpenSSL based
Python | [tlslite](http://trevp.net/tlslite/) | [requested](https://github.com/trevp/tlslite/issues/19) | 
Python | [pyOpenSSL](https://github.com/pyca/pyopenssl) | [released](0.15.0, with cryptography 0.9) | 
Go | [native](http://golang.org/pkg/crypto/tls/) | [in next release](https://code.google.com/p/go/source/detail?r=71dc1b4815f2) |
C++ | [yaSSL](http://www.wolfssl.com/yaSSL/) | [requested](https://github.com/cyassl/cyassl/issues/66) |
JavaScript | [NodeJS](http://nodejs.org/api/tls.html) | [requested](https://github.com/joyent/node/issues/5945) | waiting for stable OpenSSL
Ruby | [native](http://ruby-doc.org/stdlib-2.0/libdoc/openssl/rdoc/OpenSSL.html) | [requested](https://bugs.ruby-lang.org/issues/9390) | OpenSSL-based
C | [SChannel](http://technet.microsoft.com/en-us/library/hh831771.aspx) | released | Added in Windows 8.1 / Server 2012 R2
Java | [android](https://code.google.com/p/android/issues/detail?id=56942) | released | Added in Android 4.4  
Java | [jetty](https://github.com/jetty-project/jetty-alpn/) | released |

Note that some client implementations deal very poorly if a server sends both ALPN and NPN in the ServerHello.  When a server responds negotiating a protocol via ALPN in the ServerHello, it must not also send a list of protocols for NPN negotiation as well.
