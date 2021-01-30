# linux compiling
```
gcc -o [elf] [.c]
gcc -Wl,--hash-style=both -o [elf] [.c] # "error while loading shared libraries: requires glibc 2.5 or later dynamic linker"
gcc -static -o [elf] [.c] # or this
```
# Windows compiling (mingw-w64 + wine)
```
apt install mingw-w64
i686-w64-mingw32-gcc [.c] -o [.exe] # -lws2_32
i686-w64-mingw32-g++ [.cpp] -o [.exe]
wine [.exe]
```
# Tips

    i686-w64-mingw32-gcc -lws2_32 - https://stackoverflow.com/a/2033632/4908573
    Malloc and memcpy, allocate and initialize with null bytes - https://forums.offensive-security.com/showthread.php?2363-Fixing-643-c-script&p=9453#post9453
    Adding +1 to pointers is 4 bytes - https://stackoverflow.com/a/11598369/4908573

