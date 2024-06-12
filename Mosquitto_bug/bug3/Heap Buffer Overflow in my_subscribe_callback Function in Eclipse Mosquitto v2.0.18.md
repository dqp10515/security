# **Heap Buffer Overflow in my_subscribe_callback Function in Eclipse Mosquitto v2.0.18**

## Basic information

**Project name:** Eclipse Mosquitto **Project id:** mosquitto.org

**Request type:** publication

**Versions affected:** [v2.0.18]

**Common Weakness Enumeration:**

- [CWE-122: Heap-based Buffer Overflow](https://cwe.mitre.org/data/definitions/122.html)

**Common Vulnerability Scoring System:** -

**Summary:**

In Eclipse Mosquitto version v2.0.18-4-g15292b20, a heap-buffer-overflow vulnerability was discovered that can be triggered when the broker processes a specially crafted packet. This vulnerability exists due to a boundary error within the `my_subscribe_callback` function in the `sub_client.c` file, which can lead to code execution or denial of service.

**Reproduce：**

1. 
   
    Configure the building environment with ASan and UBSan by setting the environment variables for the compiler and linker:
    
    `export CC=clang CXX=clang++
    export CFLAGS="-g -fsanitize=address,undefined -fno-omit-frame-pointer"export CXXFLAGS="-g -fsanitize=address,undefined -fno-omit-frame-pointer"export LDFLAGS="-fsanitize=address,undefined"make clean
    make -j 8`
    
2. 
   
    Start the Mosquitto broker server in verbose mode on port 2883:
    
    `mosquitto/src/mosquitto -v -p 2883`
    
3. 
   
    Write the data transmission from the proxy to the client using a series of hexstream found in `hexstream.txt`:
    
    [hexstream.txt](CVE-2024-31037%20Information%207820c92f124f4725a0272fb9c0b6710b/hexstream.txt)
    

**Crash Report：**
Here is the client log：

`Run topic: test/tp
Run sub(Qos0) in topic [test/tp]
Run sub(Qos1) in topic [test/tp]
Run sub(Qos2) in topic [test/tp]
Run sub(Qos2) in topic [test/#]
Run Qos1 sub in topic [test/chat] with qos2 will
Hit CTRL+C
test/tp qos2
test/tp qos2
test/tp qos2
test/tp qos2`

Here is the ASan reprot:

`Error: Unknown error.
Error: The connection was lost.
=================================================================
Error: A network protocol error occurred when communicating with the broker.
==1400134==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000000210 at pc 0x0000004cb0c8 bp 0x7ffeee2abd60 sp 0x7ffeee2abd58
READ of size 4 at 0x602000000210 thread T0
    #0 0x4cb0c7 in my_subscribe_callback mosquitto/client/sub_client.c:150:27
    #1 0x7fb53b7b9b3f in handle__suback mosquitto/lib/handle_suback.c:102:3
    #2 0x7fb53b80512f in handle__packet mosquitto/lib/read_handle.c:59:11
    #3 0x7fb53b7f274a in packet__read mosquitto/lib/packet_mosq.c:566:7
    #4 0x7fb53b7c21f1 in mosquitto_loop_read mosquitto/lib/loop.c:380:9
    #5 0x7fb53b7c0966 in mosquitto_loop mosquitto/lib/loop.c:149:10
    #6 0x7fb53b7c2785 in mosquitto_loop_forever mosquitto/lib/loop.c:257:9
    #7 0x4ca20c in main mosquitto/client/sub_client.c:404:7
    #8 0x7fb53b3c60b2 in __libc_start_main /build/glibc-eX1tMB/glibc-2.31/csu/../csu/libc-start.c:308:16
    #9 0x41d76d in _start (mosquitto/client/mosquitto_sub+0x41d76d)

0x602000000211 is located 0 bytes to the right of 1-byte region [0x602000000210,0x602000000211)
allocated by thread T0 here:
    #0 0x4989aa in malloc /home/brian/src/llvm_releases/llvm-project/llvm/utils/release/final/llvm-project/compiler-rt/lib/asan/asan_malloc_linux.cpp:145:3

SUMMARY: AddressSanitizer: heap-buffer-overflow mosquitto/client/sub_client.c:150:27 in my_subscribe_callback
Shadow bytes around the buggy address:
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff8000: fa fa 00 07 fa fa 00 fa fa fa 00 02 fa fa 00 fa
  0x0c047fff8010: fa fa 00 02 fa fa 00 02 fa fa 00 fa fa fa 00 01
  0x0c047fff8020: fa fa 00 02 fa fa 00 00 fa fa fd fd fa fa fd fd
  0x0c047fff8030: fa fa 00 00 fa fa fd fa fa fa fd fd fa fa 02 fa
=>0x0c047fff8040: fa fa[01]fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8060: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8070: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8090: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07 
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==1400134==ABORTING
Error: The connection was lost.`