# **NULL Pointer Dereference in memcpy Function of database.c:729 in Eclipse Mosquitto 2.0.18**

## Basic information

**Project name:** Eclipse Mosquitto **Project id:** mosquitto.org

**Request type:** publication

**Versions affected:** [v2.0.18]

**Common Weakness Enumeration:**

- [CWE-476: NULL Pointer Dereference](https://cwe.mitre.org/data/definitions/476.html)
- [CWE-690: Unchecked Return Value to NULL Pointer Dereference](https://cwe.mitre.org/data/definitions/690.html)
- [CWE-824: Access of Uninitialized Pointer](https://cwe.mitre.org/data/definitions/824.html)

**Common Vulnerability Scoring System:** -

**Summary:**

A runtime error related to a NULL Pointer Dereference was detected in Eclipse Mosquitto version 2.0.18. The UndefinedBehaviorSanitizer (UBSan) identified the issue within the `database.c` file at line 729. The error occurred in the `memcpy` function, which was called with a null pointer as the argument. According to the C standard library, the `memcpy` function requires non-null pointers for both the source and destination.

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
    
    [hexstream.txt](CVE-2024-31039%20Information%203c9f7a10790e49e48776bffd6f6779ea/hexstream.txt)
    

**Crash Report：**

Here is the UBSan reprot:

`1711296085: Warning: Unable to drop privileges to 'mosquitto' because this user does not exist. Trying 'nobody' instead.
1711296085: mosquitto version 2.0.18 starting
1711296085: Using default config.
1711296085: Starting in local only mode. Connections will only be possible from clients running on this machine.
1711296085: Create a configuration file which defines a listener to allow remote access.
1711296085: For more details see https://mosquitto.org/documentation/authentication-methods/
1711296085: Opening ipv4 listen socket on port 2883.
1711296085: Opening ipv6 listen socket on port 2883.
1711296085: Error: Cannot assign requested address
1711296085: mosquitto version 2.0.18 running
1711296085: New connection from 127.0.0.1:58552 on port 2883.
1711296085: Client <unknown> disconnected due to protocol error.
1711296085: New connection from 127.0.0.1:58564 on port 2883.
1711296085: New connection from 127.0.0.1:58566 on port 2883.
1711296085: New connection from 127.0.0.1:58580 on port 2883.
1711296085: New connection from 127.0.0.1:58582 on port 2883.
1711296085: New connection from 127.0.0.1:58586 on port 2883.
1711296085: New client connected from 127.0.0.1:58566 as auto-60AC9CA4-3E33-B99A-B411-C0F0D891731D (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-60AC9CA4-3E33-B99A-B411-C0F0D891731D (0, 0)
1711296085: New client connected from 127.0.0.1:58580 as auto-9C04761C-042B-C427-6596-2B0F640024F7 (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-9C04761C-042B-C427-6596-2B0F640024F7 (0, 0)
1711296085: Client <unknown> disconnected due to protocol error.
1711296085: New client connected from 127.0.0.1:58586 as -54--54340282366920916984354--151011750734121385317528684059885022954a (p2, c1, k60).
1711296085: Will message specified (0 bytes) (r1, q2).
1711296085: 	test/tp
1711296085: Sending CONNACK to -54--54340282366920916984354--151011750734121385317528684059885022954a (0, 0)
1711296085: New connection from 127.0.0.1:58602 on port 2883.
1711296085: Received SUBSCRIBE from auto-9C04761C-042B-C427-6596-2B0F640024F7
1711296085: 	test/tp (QoS 0)
1711296085: auto-9C04761C-042B-C427-6596-2B0F640024F7 0 test/tp
1711296085: 	test/tp (QoS 0)
1711296085: auto-9C04761C-042B-C427-6596-2B0F640024F7 0 test/tp
1711296085: Sending SUBACK to auto-9C04761C-042B-C427-6596-2B0F640024F7
1711296085: Client auto-60AC9CA4-3E33-B99A-B411-C0F0D891731D disconnected due to malformed packet.
1711296085: Client <unknown> disconnected due to protocol error.
1711296085: New connection from 127.0.0.1:58610 on port 2883.
1711296085: New client connected from 127.0.0.1:58610 as auto-CADDE079-6B31-7570-405D-7FEB26603CD1 (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-CADDE079-6B31-7570-405D-7FEB26603CD1 (0, 0)
1711296085: Bad client auto-CADDE079-6B31-7570-405D-7FEB26603CD1 sending multiple CONNECT messages.
1711296085: Client auto-CADDE079-6B31-7570-405D-7FEB26603CD1 disconnected due to protocol error.
1711296085: New connection from 127.0.0.1:58626 on port 2883.
1711296085: New client connected from 127.0.0.1:58626 as auto-96000D92-5BC5-7247-D965-2068424301AC (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-96000D92-5BC5-7247-D965-2068424301AC (0, 0)
1711296085: Client auto-96000D92-5BC5-7247-D965-2068424301AC closed its connection.
1711296085: New connection from 127.0.0.1:58628 on port 2883.
1711296085: Client <unknown> disconnected due to protocol error.
1711296085: New connection from 127.0.0.1:58640 on port 2883.
1711296085: New client connected from 127.0.0.1:58640 as auto-91074C54-4881-91F2-72C9-2AEF6DFF6F35 (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-91074C54-4881-91F2-72C9-2AEF6DFF6F35 (0, 0)
1711296085: Received PUBLISH from auto-91074C54-4881-91F2-72C9-2AEF6DFF6F35 (d0, q1, r0, m1, 'test/tt', ... (70 bytes))
1711296085: Sending PUBACK to auto-91074C54-4881-91F2-72C9-2AEF6DFF6F35 (m1, rc0)
1711296085: Client auto-91074C54-4881-91F2-72C9-2AEF6DFF6F35 disconnected due to malformed packet.
1711296085: New connection from 127.0.0.1:58648 on port 2883.
1711296085: New client connected from 127.0.0.1:58648 as auto-59E2769E-ED0B-A806-0DDD-F4BC4B7A8265 (p2, c1, k60).
1711296085: No will message specified.
1711296085: Sending CONNACK to auto-59E2769E-ED0B-A806-0DDD-F4BC4B7A8265 (0, 0)
1711296085: Client auto-59E2769E-ED0B-A806-0DDD-F4BC4B7A8265 closed its connection.
1711296086: Client <unknown> disconnected: Success.
1711296086: Client auto-9C04761C-042B-C427-6596-2B0F640024F7 closed its connection.
1711296086: Client -54--54340282366920916984354--151011750734121385317528684059885022954a closed its connection.
database.c:729:26: runtime error: null pointer passed as argument 2, which is declared to never be null
/usr/include/string.h:44:28: note: nonnull attribute specified here
==1949874==WARNING: invalid path to external symbolizer!
==1949874==WARNING: Failed to use and restart external symbolizer!
    #0 0x591c86  (mosquitto/src/mosquitto+0x591c86)
    #1 0x552646  (mosquitto/src/mosquitto+0x552646)
    #2 0x5588d9  (mosquitto/src/mosquitto+0x5588d9)
    #3 0x5db16f  (mosquitto/src/mosquitto+0x5db16f)
    #4 0x5e32a4  (mosquitto/src/mosquitto+0x5e32a4)
    #5 0x5e237c  (mosquitto/src/mosquitto+0x5e237c)
    #6 0x5df163  (mosquitto/src/mosquitto+0x5df163)
    #7 0x5d4406  (mosquitto/src/mosquitto+0x5d4406)
    #8 0x4ce95d  (mosquitto/src/mosquitto+0x4ce95d)
    #9 0x7f4afbabb0b2  (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #10 0x41fd8d  (mosquitto/src/mosquitto+0x41fd8d)

SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior database.c:729:26 in`