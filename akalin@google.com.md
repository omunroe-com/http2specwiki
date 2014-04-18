Hello, 
I'm trying to upgrade enable draft 06 of HTTP2 on chromium. But i can't build it.
I'm using GitBASH on Windows; and the version 34.0 of chromium. 
I've followed some tutorial on how to build Chromium and i've found this 
http://www.chromium.org/developers/how-tos/build-instructions-windows  

but to build ninja i need to do this : 
https://code.google.com/p/chromium/wiki/NinjaBuild

but when i try to use : 
python build/gyp_chromium
im having this error : 
Traceback (most Recent call last):
File "build/gyp__chromium", line269 in module 
vs2013_runtime_dll_dirs = vs_toolchain.SetEnvironementAndGetRuntiimeDllDirs()
Filevs_toolchain.py, line 33, in SetEnvironementAndGetRunTimeDllDirs with open(json_data_file,'r') as tmpf:
IOError:Errno2 No such file or directory : win_toolchain.json'

Can you please help me? I can't find this file. Or tell me how can I build chromium? 
Thanks 