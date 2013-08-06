# Fred's Chromium

A build of Chrome that supports HTTP/2 

## Instructions

1. Get and build the latest Chromium source: https://code.google.com/p/chromium/wiki/UsingGit

2. Patch in (in order):
   * https://codereview.chromium.org/21820003/
   * https://codereview.chromium.org/22074002/ and
   * https://codereview.chromium.org/22159003/

3. Build Chromium with the patches.
4. Run Chrome and then on the "HTTP-draft-04/2" experiment in about:flags.