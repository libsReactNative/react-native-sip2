# Understanding: Endpoint Lifecycle

## Phase: EXPLORING

## Hypothesis

The Endpoint class is the main entry point and central coordinator for the SIP library. It:
1. Initializes the native PjSIP module
2. Subscribes to native events (registration, call, message events)
3. Exposes all SIP operations as Promise-based methods
4. Acts as an EventEmitter for state changes

Expected to find:
- Initialization flow (start method)
- Event subscription mapping
- Account and Call CRUD operations
- Network/service configuration methods

## Sources

- `src/Endpoint.js` - Main endpoint implementation (already read)
- `index.js` - Module exports

## Validated Understanding

> Updated during EXPLORING phase

### Endpoint Lifecycle Flow

**1. Construction & Event Subscription (constructor)**
```javascript
constructor() {
  super(); // EventEmitter initialization
  
  // Subscribe to Account events
  DeviceEventEmitter.addListener('pjSipRegistrationChanged', this._onRegistrationChanged.bind(this));
  
  // Subscribe to Call events
  DeviceEventEmitter.addListener('pjSipCallReceived', this._onCallReceived.bind(this));
  DeviceEventEmitter.addListener('pjSipCallChanged', this._onCallChanged.bind(this));
  DeviceEventEmitter.addListener('pjSipCallTerminated', this._onCallTerminated.bind(this));
  DeviceEventEmitter.addListener('pjSipCallScreenLocked', this._onCallScreenLocked.bind(this));
  DeviceEventEmitter.addListener('pjSipMessageReceived', this._onMessageReceived.bind(this));
  DeviceEventEmitter.addListener('pjSipConnectivityChanged', this._onConnectivityChanged.bind(this));
}
```

**2. Initialization (start method)**
```javascript
start(configuration) -> Promise<{accounts: Account[], calls: Call[], ...extra}>
```
- Calls `NativeModules.PjSipModule.start()`
- Transforms raw account/call data into model instances
- Resolves with initialized state

**3. Account Management Operations**
- `createAccount(configuration)` - Creates account, auto-registers
- `registerAccount(account, renew)` - Manual registration update
- `deleteAccount(account)` - Unregisters and removes account
- `replaceAccount(account, configuration)` - **NOT IMPLEMENTED** (throws Error)

**4. Call Operations**
- **Basic**: `makeCall()`, `answerCall()`, `hangupCall()`, `declineCall()`
- **State Control**: `holdCall()`, `unholdCall()`, `muteCall()`, `unMuteCall()`
- **Audio Routing**: `useSpeaker()`, `useEarpiece()`
- **Transfer**: `xferCall()` (blind transfer), `xferReplacesCall()` (attended transfer)
- **Redirect**: `redirectCall()` (forward call)
- **DTMF**: `dtmfCall()`
- **Signaling**: `ringingCall()`, `progressCall()`

**5. Messaging Operations**
- `sendMessage(account, destination, msg)` - Send SIP MESSAGE
- `imTyping(account, destination, isTyping)` - Send typing indicator

**6. Configuration Methods**
- `changeNetworkConfiguration(configuration)` - Network settings
- `changeServiceConfiguration(configuration)` - Service settings
- `changeCodecSettings(codecSettings)` - Codec configuration
- `changeOrientation(orientation)` - Video orientation (validates against PJMEDIA constants)
- `updateStunServers(accountId, stunServerList)` - STUN server updates
- `activateAudioSession()` / `deactivateAudioSession()` - iOS audio session control

**7. Event Emission Pattern**
All private `_on*` methods:
1. Receive native event data
2. Wrap in model classes (Account, Call, Message)
3. Emit via `this.emit(event_name, data)`

Example:
```javascript
_onRegistrationChanged(data) {
  this.emit("registration_changed", new Account(data));
}
```

## Children Identified

> Populated during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| initialization | start() method and native module setup | PENDING |
| event-subscription | DeviceEventEmitter listeners mapping | PENDING |
| account-operations | createAccount, registerAccount, deleteAccount | PENDING |
| call-operations | makeCall, answerCall, hangupCall, etc. | PENDING |
| call-control-advanced | hold, mute, transfer, redirect operations | PENDING |

## Dependencies

- **Uses**: 
  - NativeModules.PjSipModule (native bridge)
  - DeviceEventEmitter (native event subscription)
  - EventEmitter (event emission to application)
  - Account, Call, Message classes (data models)
- **Used by**: Application code integrating SIP functionality

## Key Insights

1. **Singleton Pattern Implied**: Endpoint extends EventEmitter, suggesting single instance per app
2. **No Cleanup Method**: No `removeListener` calls - potential memory leak if Endpoint is recreated
3. **Uniform Error Handling**: All native calls use `(successful, data)` callback pattern
4. **SIP URI Normalization**: Private `_normalize()` method ensures consistent URI format
5. **Incomplete Feature**: `replaceAccount()` is a stub - throws "Not implemented"
6. **Orientation Validation**: Only method with input validation (checks against PJMEDIA constants)
7. **Event Naming Convention**: Native events use camelCase with 'pjSip' prefix

## ADR Candidates

1. **EventEmitter for State**: Choice of EventEmitter over Redux/Observables
2. **Promise Wrapper Pattern**: Promises around native callbacks vs direct callbacks
3. **Constructor Event Subscription**: Automatic subscription vs manual opt-in
4. **No Cleanup API**: No destructor or dispose method for event listeners

## Flow Recommendation

- **Type**: SDD
- **Confidence**: high
- **Rationale**: Internal service API documentation, no stakeholder-facing needs

## Synthesis

> Updated during SYNTHESIZING phase

### Combined Understanding

The **Endpoint** class is the facade for the entire SIP library, providing:

1. **Initialization**: Single entry point via `start(configuration)` that bootstraps the native PjSIP module
2. **Event Hub**: Central subscription and redistribution of native events (registration, calls, messages)
3. **Operation Gateway**: All SIP operations (accounts, calls, messaging, configuration) flow through Endpoint
4. **State Transformation**: Converts raw native data into typed model objects (Account, Call, Message)

**Key Architectural Decisions**:
- EventEmitter pattern chosen for simplicity and React Native compatibility
- Promise wrapper provides consistent async API despite native callback pattern
- Automatic event subscription in constructor simplifies API but prevents cleanup
- All methods delegate to native module - JS layer is pure orchestration

### Flow Recommendation Confirmed

**Type**: SDD (Spec-Driven Development)
- Internal service API
- Needs comprehensive documentation for library consumers
- No stakeholder-facing features (no DDD)
- No test coverage found (no TDD)

## Bubble Up

> Summary to pass to parent during EXITING

- Central coordinator for all SIP operations
- Event-driven architecture with Promise-based API
- Bridges native PjSIP events to application layer
- Full call lifecycle management + messaging

---

*Phase: EXPLORING | Depth: 1 | Parent: root*
