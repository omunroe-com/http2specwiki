HTTP/2.0 prototype for Windows.

Please note following:
-	Be aware this implementation is an initial prototype.
-	Internet Explorer with HTTP/2 support is available with the Windows 10 Technical Preview located at http://blogs.msdn.com/b/ie/archive/2014/10/01/internet-explorer-and-the-windows-10-technical-preview.aspx.
-	There is also a public server located at https://h2duo.cloudapp.net/.
-	At this time, the prototype will only negotiate HTTP/2 over TLS and supports basic multiplexing and header compression.  Internet Explorer does support server pushWe will update this site when we add more features.
-	If you encounter any issues with this prototype please contact HTTP2atMS@microsoft.com.

**Update**

WinINet support HTTP2: https://msdn.microsoft.com/en-us/library/windows/desktop/aa385328.aspx  
`HTTP_PROTOCOL_FLAG_HTTP2 (0x2). Supported on Windows 10, version 1507 and later.`

WinHTTP support HTTP2: https://msdn.microsoft.com/en-us/library/windows/desktop/aa384066.aspx  
`WINHTTP_PROTOCOL_FLAG_HTTP2 (0x1). Supported on Windows 10, version 1607 and newer`
