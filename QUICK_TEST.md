# Quick Test Guide

## Build and Test

### 1. Build the Project
Build AC2D in Visual Studio (or your preferred build system).

### 2. Run the Test
Execute from command line or set as command line arguments in Visual Studio:

```
AC2DTest.exe -a testaccount -h 72.239.39.50:9000 -v testpassword
```

### 3. What to Watch For

#### Console Output - Expected Success Flow:
```
--- New Session... Connecting... ---
Received connection request from server...
Sending connect response...
Session fully connected - received game data
```

#### Console Output - Potential Issues:
- `"Dropping packet with bad checksum"` - May indicate CRC seed issues
- `"Timed out after 1000 ms"` - Server not responding, check server is running
- `"Received packet from unknown server"` - Server address mismatch
- `"WARNING: Received encrypted checksum packet but no CRC seeds yet"` - Normal during initial handshake

### 4. Verify Improvements Work

**Session State Machine**:
- Should see state transitions in console (if we add logging)
- Connection should progress smoothly through states

**Packet Validation**:
- Should NOT see "bad checksum" errors for valid packets
- Invalid packets should be rejected gracefully

**Sequence Tracking**:
- May see "Received out of order packet" messages (this is detection working)
- Should handle duplicates gracefully

### 5. If Connection Fails

**Check**:
1. Server is running and accessible
2. Firewall allows connection on port 9000
3. Account `testaccount` exists on server
4. Password `testpassword` is correct
5. Server logs for any errors

**Common Issues**:
- **Timeout**: Server not responding - check server status
- **Authentication Failed**: Check account/password on server
- **Connection Refused**: Firewall or server not listening on port 9000

### 6. Next Steps After Test

**If Successful**:
- Note any console warnings or messages
- Test entering world if character selection works
- Test movement and object loading

**If Failed**:
- Note exact error messages
- Check server logs
- Share error details for debugging

## Test Credentials Summary

- **Server**: `72.239.39.50:9000`
- **Account**: `testaccount`
- **Password**: `testpassword`
- **Command**: `AC2DTest.exe -a testaccount -h 72.239.39.50:9000 -v testpassword`

