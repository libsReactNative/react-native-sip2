# Understanding: react-native-sip2 (Root)

## Phase: EXPLORING

## Hypothesis

Based on package.json and file structure, this appears to be a React Native SIP (Session Initiation Protocol) module that provides VoIP calling functionality. The library wraps PjSIP (a native SIP stack) and exposes JavaScript APIs for:

1. **Account management** - SIP account registration and configuration
2. **Call handling** - Making/receiving audio/video calls
3. **Media rendering** - Video preview and remote video views
4. **Messaging** - SIP message handling
5. **Endpoint configuration** - SIP endpoint setup

Expected flow types:
- **SDD**: Core SIP logic (internal service)
- **VDD**: Video view components (UI-facing)
- **TDD**: If tests exist for critical SIP operations

## Sources

- `src/Account.js` - SIP account management
- `src/AccountRegistration.js` - Account registration state
- `src/Call.js` - Call operations and state
- `src/Endpoint.js` - SIP endpoint configuration
- `src/Message.js` - SIP messaging
- `src/PreviewVideoView.js` - Local video preview component
- `src/RemoteVideoView.js` - Remote video display component
- `index.js` - Module entry point and exports
- `package.json` - Dependencies and metadata
- `android/`, `ios/` - Native platform implementations

## Validated Understanding

> Updated during EXPLORING phase

This is a **React Native bridge library** for PjSIP (PJSIP), a native SIP/VoIP stack. The architecture follows a clear pattern:

### Core Architecture
1. **EventEmitter-based API**: `Endpoint` extends `EventEmitter` and serves as the main entry point
2. **Native Module Bridge**: All operations delegate to `NativeModules.PjSipModule` (iOS/Android native code)
3. **Data Models**: Plain JavaScript classes (`Account`, `Call`, `Message`, `AccountRegistration`) wrap native data
4. **Video Components**: Native UI components (`PreviewVideoView`, `RemoteVideoView`) use `requireNativeComponent`

### Key Design Patterns
- **Promise-based async API**: All operations return Promises with callback pattern `(successful, data) => {}`
- **Event-driven state changes**: Native events emitted via `DeviceEventEmitter` for registration/call/message changes
- **SIP URI normalization**: Internal `_normalize()` method handles SIP URI formatting
- **Call state tracking**: `Call` class tracks construction time for accurate duration calculations

### Module Exports
```javascript
{
  Account,           // Account data model
  Call,              // Call state and operations
  Endpoint,          // Main API (EventEmitter)
  PreviewVideoView,  // Native video preview component
  RemoteVideoView    // Native remote video component
}
```

## Children Identified

> Populated during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| endpoint-lifecycle | Endpoint initialization and event subscription | PENDING |
| account-management | SIP account CRUD and registration | PENDING |
| call-control | Call state machine and operations | PENDING |
| media-rendering | Native video view components | PENDING |
| messaging | SIP instant messaging | PENDING |

## Dependencies

- **Uses**: 
  - `react-native` (NativeModules, DeviceEventEmitter, requireNativeComponent)
  - `events.EventEmitter` (for event-driven API)
  - `prop-types` (for video component prop validation)
  - PjSIP native library (via NativeModules.PjSipModule)
- **Used by**: React Native applications requiring VoIP/SIP functionality

## Key Insights

1. **Bridge Architecture**: Pure JavaScript bridge to native PjSIP - no business logic in JS, all heavy lifting in native code
2. **Stateless Models**: `Account`, `Call`, `Message` are data wrappers with getters - no internal state mutation
3. **Event Subscription Pattern**: Endpoint subscribes to native events in constructor, emits typed events to application layer
4. **Duration Calculation Trick**: `Call` stores `_constructionTime` and adds offset to provide up-to-date duration without native calls
5. **Video Components are Passive**: `PreviewVideoView` and `RemoteVideoView` are thin wrappers around native UI components with minimal props
6. **No Error Handling**: Native callbacks return `(successful, data)` tuples - errors passed as data, no try/catch patterns
7. **Incomplete Implementation**: `replaceAccount()` throws "Not implemented" error

## ADR Candidates

1. **Native PjSIP Integration**: Choice to wrap PjSIP via React Native bridge instead of pure JS SIP library
2. **EventEmitter API Design**: Decision to use EventEmitter pattern for state changes vs observables or callbacks
3. **Video Component Architecture**: Separate native components for preview/remote video with `requireNativeComponent`
4. **Promise + Callback Pattern**: Using Promises wrapping native callbacks instead of direct callbacks

## Flow Recommendation

- **Type**: SDD (primary) + VDD (for video components)
- **Confidence**: high
- **Rationale**: 
  - SDD: Internal service library with no stakeholder-facing documentation needs
  - VDD: Video components are UI-facing but minimal (pass-through to native)
  - No tests found in codebase (no TDD indicators)
  - No stakeholder documentation patterns (no DDD indicators)

## Synthesis

> Updated during SYNTHESIZING phase after children complete

### From Children

| Child | Key Insight | Flow Decision |
|-------|-------------|---------------|
| endpoint-lifecycle | Central facade with EventEmitter + Promise pattern | flows/sdd-endpoint/ created |
| account-management | Immutable data models (Account + AccountRegistration) | Covered in sdd-endpoint |
| call-control | Comprehensive state model with duration tracking trick | Covered in sdd-endpoint |
| media-rendering | Passive native wrappers (10 LOC each) | No flow (too simple) |
| messaging | Simple message data model (8 getters) | Covered in sdd-endpoint |

### Combined Understanding

**react-native-sip2** is a React Native bridge library for PjSIP (native SIP/VoIP stack) with the following architecture:

**1. Central Facade Pattern**
- `Endpoint` class is the single entry point
- Extends EventEmitter for event-driven API
- All operations delegate to native module

**2. Immutable Data Models**
- `Account` (15 getters), `Call` (30+ getters), `Message` (8 getters)
- No setters, no state mutation
- Constructor receives raw native data

**3. Promise-Based Async API**
- All operations return Promises
- Native callbacks use `(successful, data)` pattern
- Consistent error handling

**4. Event-Driven State Changes**
- 7 native event subscriptions
- Automatic event redistribution
- Real-time state updates

**5. Native Video Components**
- `PreviewVideoView`, `RemoteVideoView`
- Pure native component wrappers
- No JavaScript logic

**6. Key Design Decisions**
- EventEmitter over Redux/Observables (simplicity)
- Timestamp-based duration calculation (performance)
- SIP URI normalization (consistency)
- No cleanup method for events (potential memory leak)

### Flow Coverage

- **flows/sdd-endpoint/**: Comprehensive SDD for Endpoint API
- **No additional flows**: Other modules are either covered or too simple

## Bubble Up

> Summary to pass to parent during EXITING

- React Native wrapper for PjSIP library
- Provides account, call, and media management APIs
- Mix of service logic (SDD) and UI components (VDD)
- Event-driven architecture with Promise-based async operations

---

*Phase: COMPLETE | Depth: 0 | Parent: none*
