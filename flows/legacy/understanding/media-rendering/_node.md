# Understanding: Media Rendering

## Phase: EXPLORING

## Hypothesis

Video components are thin wrappers around native UI components. Expected to find minimal JavaScript logic.

## Sources

- `src/PreviewVideoView.js` - Local video preview (already read)
- `src/RemoteVideoView.js` - Remote video display (already read)

## Validated Understanding

> Updated during EXPLORING phase

### PreviewVideoView

**Purpose**: Display local camera preview during calls.

**Implementation**:
```javascript
const View = requireNativeComponent('PjSipPreviewVideoView', null);
export default View;
```

**Props** (via PropTypes):
- `deviceId` (number, required) - Camera/device identifier
- `objectFit` ('contain' | 'cover') - Video scaling mode

**Characteristics**:
- Pure native component wrapper
- No JavaScript logic
- Uses React Native's `requireNativeComponent`
- Props validated with PropTypes

### RemoteVideoView

**Purpose**: Display remote participant's video stream.

**Implementation**:
```javascript
const View = requireNativeComponent('PjSipRemoteVideoView', null);
export default View;
```

**Props** (via PropTypes):
- `windowId` (string, required) - Remote video window/stream identifier
- `objectFit` ('contain' | 'cover') - Video scaling mode

**Characteristics**:
- Pure native component wrapper
- No JavaScript logic
- Uses React Native's `requireNativeComponent`
- Props validated with PropTypes

### Common Pattern

Both components:
1. Import `requireNativeComponent` from react-native
2. Import PropTypes for type validation
3. Define metadata object (name, propTypes)
4. Create native view wrapper
5. Export default view

**No JavaScript business logic** - all rendering handled by native code.

## Children Identified

> Populated during SPAWNING phase

[No children - these are passive component wrappers]

## Dependencies

- **Uses**: 
  - react-native (requireNativeComponent, PropTypes)
- **Used by**: Application UI code, call screen components

## Key Insights

1. **Zero JS Logic**: Components are pure pass-through to native
2. **Minimal API**: Only 2 props per component
3. **Type Safety**: PropTypes for runtime validation
4. **Naming Convention**: PjSip prefix for native components
5. **objectFit Common**: Both support 'contain'/'cover' scaling

## ADR Candidates

- **Native Components**: Video rendering in native vs WebRTC in JS
- **Minimal Wrapper Pattern**: Thin wrappers vs full-featured JS components

## Flow Recommendation

- **Type**: VDD (Visual-Driven Development) - but minimal value
- **Confidence**: low
- **Rationale**: Components are too simple to warrant separate VDD flow
- **Decision**: Document as part of larger call UI flow (not created yet)

## Synthesis

> Updated during SYNTHESIZING phase

### Combined Understanding

**Media Rendering** consists of two **passive native component wrappers**:

1. **PreviewVideoView**: Local camera preview (deviceId prop)
2. **RemoteVideoView**: Remote video display (windowId prop)

Both are:
- 10 lines of code each
- No JavaScript logic
- Pure React Native native component wrappers
- PropTypes validated

**No separate flow needed** - too trivial to warrant VDD documentation.

## Bubble Up

> Summary to pass to parent during EXITING

- PreviewVideoView: Native preview component (deviceId prop)
- RemoteVideoView: Native remote video (windowId prop)
- Both are passive wrappers, no JS logic
- No separate flow needed (too simple)

---

*Phase: EXPLORING | Depth: 1 | Parent: root*
