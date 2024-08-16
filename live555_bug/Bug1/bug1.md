**Subject:** Recurring Crash in `MultiFramedRTPSink::sendPacketIfNecessary` Leading to Stack Overflow

**Description:** A recurring crash occurs within the `MultiFramedRTPSink::sendPacketIfNecessary` function during the streaming process, leading to a stack overflow. Detailed information regarding the crash is provided below:

**Crash Description:**

- The crash happens repeatedly during streaming.
- The issue appears to be caused by an infinite recursion between `MultiFramedRTPSink::sendPacketIfNecessary` and `MP3FileSource::doGetNextFrame`.
- The program outputs "Beginning streaming..." and "...done streaming" multiple times before the crash occurs.
- The stack overflow is detected by AddressSanitizer.

**AddressSanitizer Output:**

```
Beginning streaming...
...done streaming (repeated several times)
AddressSanitizer:DEADLYSIGNAL
=================================================================
==571607==ERROR: AddressSanitizer: stackoverflow on address 0x7fff3d5fa940 (pc 0x0000004e487b bp 0x7fff3d60aaf0 sp 0x7fff3d5fa920 T0)
#0 0x4e487b in MultiFramedRTPSink::sendPacketIfNecessary() live555/liveMedia/MultiFramedRTPSink.cpp:367
#1 0x4cf3f9 in MP3FileSource::doGetNextFrame() live555/liveMedia/MP3FileSource.cpp:1225
#2 0x4e34ba in MultiFramedRTPSink::packFrame() live555/liveMedia/MultiFramedRTPSink.cpp:22314
... 
(repeated calls between MultiFramedRTPSink::sendPacketIfNecessary and MP3FileSource::doGetNextFrame)
...
SUMMARY: AddressSanitizer: stackoverflow live555/liveMedia/MultiFramedRTPSink.cpp:367 in MultiFramedRTPSink::sendPacketIfNecessary()
==571607==ABORTING
```

**Command Used to Start:**

- `testProgs/testMP3Streamer`

**Steps to Reproduce:**

1. Start the `testMP3Streamer` using the command mentioned above.
2. Begin streaming an MP3 file.
3. Observe the crash after multiple "Beginning streaming..." and "...done streaming" outputs.