# AC2D Integration Plan: bzr + ACClientLib

## Overview
This document outlines the plan to integrate improvements from the **bzr** (Bael'Zharon's Respite) client project and potentially leverage **ACClientLib** to enhance AC2D's ability to connect to **online ACEmulator servers**.

**Important Note**: bzr is described as a good "offline client", so we must be selective - only pulling networking protocol improvements that work for **online servers**, not offline-specific features.

## Target: ACEmulator Server Compatibility

AC2D needs to work with **ACEmulator** (online server emulator). Key requirements:
- ✅ Password authentication support (already implemented: `kAuthAccountPassword`)
- ✅ Proper connection handshake (Connect Request/Response)
- ✅ Encrypted checksums with CRC seeds
- ✅ Proper blob fragment handling
- ✅ Sequence number tracking and retransmission
- ✅ World server redirects

## Key Improvements from bzr

### 1. Session State Management ✅ HIGH PRIORITY
**Current AC2D**: Uses flags (`SF_CONNECTED`, `SF_CRCSEEDS`) to track state
**bzr Approach**: Explicit state machine with states:
- `kLogon` - Initial connection attempt
- `kReferred` - Server referral received
- `kConnectResponse` - Waiting for connection confirmation
- `kConnected` - Fully connected

**Benefits**:
- Clearer state transitions
- Better error handling
- Easier debugging

### 2. Blob Fragment Assembly ✅ HIGH PRIORITY
**Current AC2D**: Basic fragment handling in `cMessage`
**bzr Approach**: Dedicated `BlobAssembler` class with:
- Bitmask tracking of received fragments
- Proper validation of fragment counts and sizes
- Clean separation of concerns

**Benefits**:
- More reliable fragment reassembly
- Better handling of out-of-order fragments
- Reduced memory fragmentation

### 3. Packet Validation ✅ MEDIUM PRIORITY
**Current AC2D**: Basic checksum validation
**bzr Approach**:
- Comprehensive checksum validation before processing
- Packet size validation
- Sequence number validation
- NetId and iteration validation

**Benefits**:
- More secure connection
- Better detection of corrupted packets
- Protection against replay attacks

### 4. Sequence Number Tracking ✅ MEDIUM PRIORITY
**Current AC2D**: Single sequence number per server
**bzr Approach**: Dual tracking:
- `serverSequence_` - Last acknowledged sequence
- `serverLeadingSequence_` - Latest contiguous sequence received
- `clientSequence_` - Last client sequence acknowledged by server
- `clientLeadingSequence_` - Latest client sequence sent

**Benefits**:
- Better handling of out-of-order packets
- More accurate retransmission requests
- Improved connection reliability

### 5. Retransmission Handling ✅ MEDIUM PRIORITY
**Current AC2D**: Basic retransmission on request
**bzr Approach**:
- Automatic detection of missing packets
- Proper retransmission request generation
- Packet caching for retransmission

**Benefits**:
- Better handling of packet loss
- More reliable connections on poor networks
- Automatic recovery from network issues

### 6. Error Handling ✅ LOW PRIORITY
**Current AC2D**: Basic error messages
**bzr Approach**:
- Timeout handling with retry limits
- Better error reporting
- Graceful connection failure handling

## ACClientLib Integration

**ACClientLib** (https://gitlab.com/trevis/acclientlib) appears to be a modular library. Potential uses:
- As a reference implementation
- For specific components if compatible
- For testing against different server implementations

**Action Items**:
1. Review ACClientLib source code structure
2. Identify reusable components
3. Assess compatibility with AC2D architecture
4. Determine if library integration or code reference is better

## Implementation Strategy

### Phase 1: Critical Networking Improvements
1. ✅ Improve session state management
2. ✅ Enhance blob fragment handling
3. ✅ Better packet validation

### Phase 2: Reliability Improvements
4. ✅ Better sequence number tracking
5. ✅ Improved retransmission logic
6. ✅ Enhanced error handling

### Phase 3: Testing & Refinement
7. Test against ACE servers
8. Performance optimization
9. Bug fixes

## Code Compatibility Notes

### Structure Differences
- **AC2D**: `stTransitHeader` (20 bytes) - matches bzr's `PacketHeader`
- **AC2D**: `stFragmentHeader` (16 bytes) - similar to bzr's `FragmentHeader` but field names differ
- **bzr**: Uses `uint64_t id` for fragment tracking vs AC2D's `DWORD m_dwSequence`

### Integration Approach
- **ONLY** pull networking protocol improvements that work for online servers
- **AVOID** offline-specific features from bzr (file-based data, local-only features, etc.)
- Adapt bzr's networking concepts to AC2D's existing structure
- Maintain AC2D's Windows-specific code where appropriate
- Use bzr's algorithms and state management patterns for online connections
- Keep AC2D's existing UI and graphics systems
- Focus on ACEmulator protocol compatibility

## Next Steps

1. Start with session state management improvements
2. Integrate better blob fragment handling
3. Add comprehensive packet validation
4. Test with ACE servers
5. Iterate based on results

