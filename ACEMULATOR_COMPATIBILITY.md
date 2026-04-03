# ACEmulator Compatibility Guide

## Overview
This document tracks AC2D's compatibility with **ACEmulator** (online server emulator) and ensures we're pulling the right features from bzr (offline client) for online server connections.

## ACEmulator Requirements

### Authentication
- ✅ **Password Authentication**: AC2D supports `kAuthAccountPassword` (line 497 in cNetwork.cpp)
- ✅ **Account/Password**: Command line `-a account -v password` works
- ❌ **GLS Tickets**: Not used by ACEmulator (only for official Turbine servers)

### Connection Protocol
- ✅ **Logon Packet**: Sends "1802" magic string (line 488)
- ✅ **Connect Request/Response**: Handshake implemented (lines 846-933)
- ✅ **CRC Seeds**: Encrypted checksum support (lines 866-890)
- ✅ **Cookie Exchange**: Connection cookie handling (line 862)

### Networking Features
- ✅ **Session State Machine**: Now implemented (kSessionLogon → kSessionConnected)
- ✅ **Packet Validation**: Checksum and size validation added
- ✅ **Sequence Tracking**: Leading vs acknowledged sequence tracking
- 🔄 **Blob Fragments**: Basic support, could be improved
- 🔄 **Retransmission**: Basic support, needs improvement

## What to Pull from bzr (Online-Compatible Only)

### ✅ Safe to Use (Protocol-Level)
1. **Session State Management** - Works for any server (online or offline)
2. **Packet Validation** - Protocol-level validation, server-agnostic
3. **Sequence Number Tracking** - Standard networking pattern
4. **Blob Fragment Assembly** - Protocol feature, not server-specific
5. **Retransmission Logic** - Standard reliability feature

### ✅ Good: Local DAT Files for Static Assets
**AC2D already does this correctly!** The client should read static game assets from local DAT files:
- ✅ **Models** - Loaded from `client_portal.dat` (see `cPortal.cpp` line 33)
- ✅ **Textures** - Loaded from local DAT files
- ✅ **Animations** - Loaded from local DAT files  
- ✅ **Palettes** - Loaded from local DAT files
- ✅ **Sounds** - Loaded from local DAT files

**Why this is good**: Reduces server load by not requesting static assets over the network. The server only sends dynamic game state (object positions, player actions, etc.).

### ❌ Avoid (Offline-Specific - Bypassing Server for Game State)
1. **Game state from files** - Loading object positions, player data, or world state from files instead of server
2. **Bypassing server communication** - Features that don't require server connection for dynamic data
3. **Offline world generation** - Features that create or modify world data locally without server
4. **Cache-only game modes** - Running the game without server connection for game state
5. **Local-only object management** - Managing objects locally without server synchronization

## Current AC2D Status for ACEmulator

### Working ✅
- Password authentication (`-a account -v password`)
- Connection handshake (Connect Request/Response)
- Encrypted checksums with CRC seeds
- Basic packet processing
- World server communication
- **Local DAT file loading** - Static assets (models, textures) loaded from local files (reduces server load)

### Needs Improvement 🔄
- Blob fragment reassembly (can drop fragments on poor networks)
- Retransmission handling (missing packets not automatically requested)
- Out-of-order packet handling (packets stored but not replayed)
- Error recovery (timeouts and reconnection)

### Testing Checklist
- [ ] Connect to ACEmulator server with account/password
- [ ] Complete login and character selection
- [ ] Enter world and receive game data
- [ ] Handle network interruptions gracefully
- [ ] Recover from packet loss
- [ ] Handle server redirects properly

### Test Server
**Server Address**: `72.239.39.50:9000`  
**Test Account**: `testaccount`  
**Test Password**: `testpassword`  
**Connection Command**: 
```
AC2DTest.exe -a testaccount -h 72.239.39.50:9000 -v testpassword
```

See `TESTING_GUIDE.md` for detailed testing procedures.

## Notes from Code

### ACEmulator Compatibility
Line 489 in `cNetwork.cpp`:
```cpp
// Data length left in packet including ticket (FIXME: calculate this, but ACEmulator doesn't seem to care)
```

This suggests AC2D has been tested with ACEmulator and it's more lenient than official servers. This is good - we have some flexibility.

### Local DAT File Usage
AC2D correctly uses local DAT files for static assets:
- `cPortal.cpp` line 33: Loads `client_portal.dat` for models, textures, animations, etc.
- `cPortal.cpp` line 38-45: Also loads `client_highres.dat` if available
- `cTurbineFile.cpp`: Handles file mapping and reading from DAT files

This is the **correct pattern** - static assets from local files, dynamic game state from server. This reduces server load while maintaining proper server authority over game state.

## Next Steps for ACEmulator Compatibility

1. **Improve Blob Fragment Handling** - Critical for reliable game data reception
2. **Add Automatic Retransmission** - Request missing packets automatically
3. **Better Error Messages** - Help users debug connection issues
4. **Connection Resilience** - Handle disconnects and reconnects gracefully
5. **Test with Real ACEmulator Server** - Verify all features work

