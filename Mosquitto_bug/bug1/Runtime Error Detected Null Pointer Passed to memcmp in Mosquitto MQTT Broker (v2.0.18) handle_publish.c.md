# **Runtime Error Detected: Null Pointer Passed to memcmp in Mosquitto MQTT Broker (v2.0.18) handle_publish.c**

## Basic information

**Project name:** Eclipse Mosquitto **Project id:** mosquitto.org

**Request type:** publication

**Versions affected:** [v2.0.18]

**Common Weakness Enumeration:**

- [CWE-476: NULL Pointer Dereference](https://cwe.mitre.org/data/definitions/476.html)
- [CWE-908: Use of Uninitialized Resource](https://cwe.mitre.org/data/definitions/908.html)
- [CWE-824: Access of Uninitialized Pointer](https://cwe.mitre.org/data/definitions/824.html)

**Common Vulnerability Scoring System:** -

**Summary:**

During the execution of Mosquitto MQTT broker version v2.0.18, an issue was identified by the UndefinedBehaviorSanitizer (UBSan). UBSan reported a runtime error within the `handle_publish.c` file, specifically at line 298, where a null pointer was passed to the `memcmp` function which expects a non-null argument, as declared in the standard library header `<string.h>`.

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
    
    [hexstream.txt](CVE-2024-31038%20Information%2063ca6b79f9ed4756b287e5ba89264c77/hexstream.txt)
    
- Crash Report：

Here is the UBSan reprot:

`1711294559: Warning: Unable to drop privileges to 'mosquitto' because this user does not exist. Trying 'nobody' instead.
1711294559: mosquitto version 2.0.18 starting
1711294559: Using default config.
1711294559: Starting in local only mode. Connections will only be possible from clients running on this machine.
1711294559: Create a configuration file which defines a listener to allow remote access.
1711294559: For more details see https://mosquitto.org/documentation/authentication-methods/
1711294559: Opening ipv4 listen socket on port 2883.
1711294559: Opening ipv6 listen socket on port 2883.
1711294559: Error: Cannot assign requested address
1711294559: mosquitto version 2.0.18 running
1711294559: New connection from 127.0.0.1:36924 on port 2883.
1711294559: New connection from 127.0.0.1:36940 on port 2883.
1711294559: New connection from 127.0.0.1:36944 on port 2883.
1711294559: New connection from 127.0.0.1:36958 on port 2883.
1711294559: New connection from 127.0.0.1:36970 on port 2883.
1711294559: New connection from 127.0.0.1:36976 on port 2883.
1711294559: New client connected from 127.0.0.1:36924 as auto-678EC0FF-E4C7-44EC-13BB-DF84E11B52AF (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-678EC0FF-E4C7-44EC-13BB-DF84E11B52AF (0, 0)
1711294559: New client connected from 127.0.0.1:36940 as auto-74BD2F9E-9087-277D-64FE-028F159AE435 (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-74BD2F9E-9087-277D-64FE-028F159AE435 (0, 0)
1711294559: New client connected from 127.0.0.1:36944 as auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD (0, 0)
1711294559: New client connected from 127.0.0.1:36958 as auto-E8F91364-15F8-E6E7-630F-63261E5934EE (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-E8F91364-15F8-E6E7-630F-63261E5934EE (0, 0)
1711294559: New client connected from 127.0.0.1:36970 as auto-3A7E449B-9663-0787-401F-2F2EC6900CDF (p2, c1, k60).
1711294559: Will message specified (9 bytes) (r1, q2).
1711294559: 	test/tp
1711294559: Sending CONNACK to auto-3A7E449B-9663-0787-401F-2F2EC6900CDF (0, 0)
1711294559: New client connected from 127.0.0.1:36976 as auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91 (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91 (0, 0)
1711294559: Received PUBLISH from auto-678EC0FF-E4C7-44EC-13BB-DF84E11B52AF (d0, q0, r0, m0, 'test/tp', ... (4 bytes))
1711294559: Received DISCONNECT from auto-678EC0FF-E4C7-44EC-13BB-DF84E11B52AF
1711294559: Client auto-678EC0FF-E4C7-44EC-13BB-DF84E11B52AF disconnected.
1711294559: New connection from 127.0.0.1:36982 on port 2883.
1711294559: Received SUBSCRIBE from auto-74BD2F9E-9087-277D-64FE-028F159AE435
1711294559: 	test/tp (QoS 0)
1711294559: auto-74BD2F9E-9087-277D-64FE-028F159AE435 0 test/tp
1711294559: Sending SUBACK to auto-74BD2F9E-9087-277D-64FE-028F159AE435
1711294559: Received SUBSCRIBE from auto-E8F91364-15F8-E6E7-630F-63261E5934EE
1711294559: 	test/tp (QoS 1)
1711294559: auto-E8F91364-15F8-E6E7-630F-63261E5934EE 1 test/tp
1711294559: Sending SUBACK to auto-E8F91364-15F8-E6E7-630F-63261E5934EE
1711294559: Received SUBSCRIBE from auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD
1711294559: 	test/tp (QoS 2)
1711294559: auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD 2 test/tp
1711294559: 	test/tp (QoS 2)
1711294559: auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD 2 test/tp
1711294559: Sending SUBACK to auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD
1711294559: Client auto-3A7E449B-9663-0787-401F-2F2EC6900CDF closed its connection.
1711294559: Sending PUBLISH to auto-74BD2F9E-9087-277D-64FE-028F159AE435 (d0, q0, r0, m0, 'test/tp', ... (9 bytes))
1711294559: Sending PUBLISH to auto-E8F91364-15F8-E6E7-630F-63261E5934EE (d0, q1, r0, m1, 'test/tp', ... (9 bytes))
1711294559: Sending PUBLISH to auto-0CDF564E-121D-6359-2F87-9A9E64CC4BAD (d0, q2, r0, m1, 'test/tp', ... (9 bytes))
1711294559: Received SUBSCRIBE from auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91
1711294559: 	test/# (QoS 2)
1711294559: auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91 2 test/#
1711294559: Sending SUBACK to auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91
1711294559: Sending PUBLISH to auto-F91BAD0D-3CB3-C4FC-3F48-2F5793AF6F91 (d0, q2, r1, m1, 'test/tp', ... (9 bytes))
1711294559: Client <unknown> disconnected due to protocol error.
1711294559: Client auto-E8F91364-15F8-E6E7-630F-63261E5934EE closed its connection.
1711294559: New connection from 127.0.0.1:36996 on port 2883.
1711294559: New client connected from 127.0.0.1:36996 as auto-54DEE642-1EB4-CFE9-CF2C-8C32C513E3E3 (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-54DEE642-1EB4-CFE9-CF2C-8C32C513E3E3 (0, 0)
1711294559: Bad client auto-54DEE642-1EB4-CFE9-CF2C-8C32C513E3E3 sending multiple CONNECT messages.
1711294559: Client auto-54DEE642-1EB4-CFE9-CF2C-8C32C513E3E3 disconnected due to protocol error.
1711294559: New connection from 127.0.0.1:37004 on port 2883.
1711294559: New client connected from 127.0.0.1:37004 as auto-4300DE1C-76A7-51A6-57E9-F6918D0EF796 (p2, c1, k60).
1711294559: No will message specified.
1711294559: Sending CONNACK to auto-4300DE1C-76A7-51A6-57E9-F6918D0EF796 (0, 0)
1711294559: Received PUBLISH from auto-4300DE1C-76A7-51A6-57E9-F6918D0EF796 (d0, q2, r0, m1, 'test/tp', ... (0 bytes))
1711294559: Sending PUBREC to auto-4300DE1C-76A7-51A6-57E9-F6918D0EF796 (m1, rc0)
1711294559: Received PUBLISH from auto-4300DE1C-76A7-51A6-57E9-F6918D0EF796 (d0, q2, r0, m1, 'test/tp', ... (0 bytes))
handle_publish.c:298:15: runtime error: null pointer passed as argument 1, which is declared to never be null
/usr/include/string.h:65:33: note: nonnull attribute specified here
==1769090==WARNING: invalid path to external symbolizer!
==1769090==WARNING: Failed to use and restart external symbolizer!
    #0 0x5c5acc  (mosquitto/src/mosquitto+0x5c5acc)
    #1 0x66c93c  (mosquitto/src/mosquitto+0x66c93c)
    #2 0x60fe3c  (mosquitto/src/mosquitto+0x60fe3c)
    #3 0x5e327f  (mosquitto/src/mosquitto+0x5e327f)
    #4 0x5e237c  (mosquitto/src/mosquitto+0x5e237c)
    #5 0x5df163  (mosquitto/src/mosquitto+0x5df163)
    #6 0x5d4406  (mosquitto/src/mosquitto+0x5d4406)
    #7 0x4ce95d  (mosquitto/src/mosquitto+0x4ce95d)
    #8 0x7f143f6350b2  (/lib/x86_64-linux-gnu/libc.so.6+0x270b2)
    #9 0x41fd8d  (mosquitto/src/mosquitto+0x41fd8d)

SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior handle_publish.c:298:15 in`