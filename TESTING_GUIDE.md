# AC2D Testing Guide for ACEmulator

## Test Server Information

**Server Address**: `72.239.39.50:9000`  
**Server Type**: ACEmulator (local test server)

## Connection Command

To connect to the test server, run AC2D with:

```
AC2DTest.exe -a testaccount -h 72.239.39.50:9000 -v testpassword
```

**Test Credentials**:
- Account: `testaccount`
- Password: `testpassword`
- Server: `72.239.39.50:9000`

## Testing Checklist

### Phase 1: Basic Connection ✅
- [ ] Client starts without errors
- [ ] Network socket binds successfully
- [ ] Login packet sent to server
- [ ] Server responds with Connect Request
- [ ] Client sends Connect Response
- [ ] Session state transitions: `kSessionLogon` → `kSessionConnectResponse` → `kSessionConnected`

### Phase 2: Authentication ✅
- [ ] Password authentication works (`kAuthAccountPassword`)
- [ ] Account name sent correctly
- [ ] Password sent in correct format (32L format)
- [ ] Server accepts credentials
- [ ] No authentication errors

### Phase 3: Packet Validation ✅
- [ ] Packet checksums validate correctly
- [ ] Packet size validation works
- [ ] Invalid packets are rejected (if any sent)
- [ ] Duplicate packets are detected and handled
- [ ] Out-of-order packets are detected

### Phase 4: Game Data Reception 🔄
- [ ] Blob fragments received and reassembled
- [ ] Game messages processed correctly
- [ ] Character list received (0xF658)
- [ ] Object creation messages (0xF745)
- [ ] Position updates received
- [ ] No fragment loss on stable connection

### Phase 5: Reliability Testing 🔄
- [ ] Missing packets detected
- [ ] Retransmission requests sent (if implemented)
- [ ] Connection survives brief network interruptions
- [ ] Reconnection works after disconnect
- [ ] Sequence numbers track correctly

### Phase 6: World Entry 🔄
- [ ] Character selection works
- [ ] Enter world request (0xF7C8) sent
- [ ] Enter world confirmation (0xF7DF) received
- [ ] Character enters world successfully
- [ ] Objects render correctly
- [ ] Movement works

## Debugging Tips

### Enable Console Output
Check the console output for:
- Connection state transitions
- Packet validation messages
- Sequence number tracking
- Error messages

### Common Issues

**Connection Timeout**
- Check server is running
- Verify IP address and port
- Check firewall settings
- Verify account exists on server

**Authentication Failed**
- Verify account name and password
- Check server logs for authentication errors
- Ensure password format is correct (32L)

**Packet Validation Errors**
- Check if CRC seeds are initialized
- Verify checksum calculation
- Check for packet corruption

**Fragment Loss**
- Monitor blob fragment assembly
- Check for out-of-order fragments
- Verify fragment count matches

## Test Scenarios

### Scenario 1: Clean Connection
1. Start server
2. Start client with test account
3. Verify full connection flow
4. Enter world
5. Move around and verify objects load

### Scenario 2: Network Interruption
1. Connect successfully
2. Briefly disconnect network
3. Reconnect network
4. Verify client recovers or reconnects

### Scenario 3: Packet Loss Simulation
1. Connect successfully
2. Monitor for missing sequence numbers
3. Verify retransmission requests (if implemented)
4. Verify missing packets are recovered

### Scenario 4: Multiple Connections
1. Connect first client
2. Connect second client with different account
3. Verify both connections work independently
4. Verify no interference between connections

## Expected Console Output

### Successful Connection
```
--- New Session... Connecting... ---
Received connection request from server...
Sending connect response...
Session fully connected - received game data
```

### State Transitions
- `kSessionLogon` - Initial state
- `kSessionConnectResponse` - After Connect Request received
- `kSessionConnected` - After first blob fragment received

### Packet Validation
- "Dropping packet with bad checksum" - Should NOT appear for valid packets
- "Received duplicate packet" - May appear, should be handled gracefully
- "Received out of order packet" - May appear, should be handled gracefully

## Server-Side Verification

On the ACEmulator server, check:
- Client connection appears in server logs
- Authentication succeeds
- Packets are received and processed
- No errors in server logs

## Next Steps After Testing

1. **Document Issues**: Note any connection problems or errors
2. **Verify Improvements**: Confirm our networking improvements work
3. **Identify Gaps**: Find areas that need more work
4. **Iterate**: Fix issues and test again

## Notes

- Test server: `72.239.39.50:9000`
- Keep server logs available for debugging
- Test with different account types if available
- Test with different network conditions if possible

