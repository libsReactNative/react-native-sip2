# Understanding: Call Control

## Phase: EXPLORING

## Hypothesis

Call class represents the state and operations of a SIP call session. Already analyzed in Endpoint.js context.

## Sources

- `src/Call.js` - Call data model (already read)

## Validated Understanding

> Updated during EXPLORING phase

### Call Model

**Purpose**: Represents a SIP call session with full state tracking.

**Constructor Parameters** (destructuring from object):
- `id`, `callId`, `accountId` - Identification
- `localContact`, `localUri`, `remoteContact`, `remoteUri` - SIP URIs
- `state`, `stateText`, `held`, `muted`, `speaker` - State flags
- `connectDuration`, `totalDuration` - Duration tracking
- `remoteOfferer`, `remoteAudioCount`, `remoteVideoCount` - Media info
- `audioCount`, `videoCount` - Stream counts
- `lastStatusCode`, `lastReason` - Last SIP response
- `media`, `provisionalMedia` - Media state

**URI Parsing Logic**:
```javascript
// Extract name and number from URI
let match = remoteUri.match(/"([^"]+)" <sip:([^@]+)@/);
if (match) {
  remoteName = match[1];
  remoteNumber = match[2];
} else {
  match = remoteUri.match(/sip:([^@]+)@/);
  if (match) remoteNumber = match[1];
}
```

Also handles `tel:` URIs for local URI.

**Duration Calculation Trick**:
```javascript
this._constructionTime = Math.round(new Date().getTime() / 1000);

getTotalDuration() {
  let time = Math.round(new Date().getTime() / 1000);
  let offset = time - this._constructionTime;
  return this._totalDuration + offset;
}
```
- Stores construction timestamp
- Adds offset to base duration for real-time updates
- Avoids native calls for duration queries

**Getters** (30+ methods):

**Identification**:
- `getId()` - Call ID (number)
- `getAccountId()` - Account ID (number)
- `getCallId()` - Dialog Call-ID string

**Duration**:
- `getTotalDuration()` - Total duration in seconds (live)
- `getConnectDuration()` - Connected duration (0 if not established)
- `getFormattedTotalDuration()` - "MM:SS" or "HH:MM:SS" format
- `getFormattedConnectDuration()` - "MM:SS" or "HH:MM:SS" format

**URIs**:
- `getLocalContact()` - Local contact URI
- `getLocalUri()` - Local SIP URI
- `getRemoteContact()` - Remote contact URI
- `getRemoteUri()` - Remote SIP URI
- `getRemoteName()` - Callee name (or null)
- `getRemoteNumber()` - Callee number
- `getRemoteFormattedNumber()` - Formatted: "Name <number>" or just number

**State**:
- `getState()` - PJSIP_INV_STATE_* constant
- `getStateText()` - Human-readable state
- `isHeld()` - On hold flag
- `isMuted()` - Muted flag
- `isSpeaker()` - Speaker mode flag
- `isTerminated()` - Call ended flag

**Media**:
- `getRemoteOfferer()` - Boolean - was remote SDP offerer
- `getRemoteAudioCount()` - Remote audio streams offered
- `getRemoteVideoCount()` - Remote video streams offered
- `getAudioCount()` - Active audio streams (0 = disabled)
- `getVideoCount()` - Active video streams (0 = disabled)
- `getMedia()` - Current media state
- `getProvisionalMedia()` - Provisional media state

**Status**:
- `getLastStatusCode()` - Last SIP status code (100-699)
- `getLastReason()` - Reason phrase

**Private**:
- `_formatTime(seconds)` - Format to "MM:SS" or "HH:MM:SS"

**Call States** (PJSIP_INV_STATE_*):
- `NULL` - Before INVITE sent/received
- `CALLING` - After INVITE sent
- `INCOMING` - After INVITE received
- `EARLY` - After response with To tag
- `CONNECTING` - After 2xx sent/received
- `CONFIRMED` - After ACK sent/received
- `DISCONNECTED` - Session terminated

**Status Codes** (comprehensive list in comments):
- 1xx: Informational (100 Trying, 180 Ringing, 183 Progress)
- 2xx: Success (200 OK)
- 3xx: Redirection
- 4xx: Client Error (400-493)
- 5xx: Server Error (500-580)
- 6xx: Global Failure (600-606)

## Children Identified

> Populated during SPAWNING phase

[No children - Call is a data model, no deeper concepts]

## Dependencies

- **Uses**: None (pure data model)
- **Used by**: Endpoint (call operations), application code

## Key Insights

1. **Duration Calculation Pattern**: Uses timestamp offset to avoid native calls - clever optimization
2. **URI Parsing**: Regex-based parsing for SIP URIs with fallback for different formats
3. **Comprehensive State**: Tracks everything - media, signaling, timing
4. **Documentation**: Extensive JSDoc with state machine and status code lists
5. **Immutable**: No setters, only getters - snapshot from native
6. **TODO Comments**: Several getters have TODO comments (missing examples)

## ADR Candidates

- **Timestamp-based Duration**: Live calculation vs polling native module
- **Comprehensive State Object**: Single object with all call info vs separate queries

## Flow Recommendation

- **Type**: SDD (already covered in flows/sdd-endpoint/)
- **Confidence**: high
- **Rationale**: Call model is integral part of Endpoint API

## Synthesis

> Updated during SYNTHESIZING phase

### Combined Understanding

**Call** is a comprehensive immutable data model representing a SIP call session:

- **30+ getters** for every aspect of call state
- **Duration tracking** via timestamp offset (no native polling)
- **URI parsing** for extracting name/number from SIP URIs
- **State machine** with 7 states (NULL → DISCONNECTED)
- **Media tracking** for audio/video stream counts
- **Status codes** full RFC 3261 range (100-699)

**No separate flow needed** - documented in flows/sdd-endpoint/

## Bubble Up

> Summary to pass to parent during EXITING

- Call: Comprehensive call state model (30+ getters)
- Duration calculation via timestamp offset
- URI parsing for name/number extraction
- Already documented in flows/sdd-endpoint/

---

*Phase: EXPLORING | Depth: 1 | Parent: root*
