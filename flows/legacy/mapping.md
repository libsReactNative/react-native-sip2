# Code to Flow Mapping

## Overview

Maps analyzed code modules to generated flows.

## Flow Type Detection Rules

| Indicator | Flow Type |
|-----------|-----------|
| `*.test.*`, `*.spec.*`, `__tests__/` | TDD |
| `components/`, `*.tsx`, `*.vue`, `templates/` | VDD |
| `README.md`, public exports, API docs | DDD |
| Internal logic, no UI, no public API | SDD |
| Architectural decisions | ADR |

## Mapping Table

| Code Path | Flow | Type | Action | Status | Notes |
|-----------|------|------|--------|--------|-------|
| src/Endpoint.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Central API, comprehensive documentation |
| src/Endpoint.js | flows/adr-002-eventemitter/ | ADR | CREATED | DRAFT | EventEmitter pattern decision |
| src/Endpoint.js | flows/adr-003-promise-pattern/ | ADR | CREATED | DRAFT | Promise wrapper decision |
| src/Account.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Account model documented in Endpoint spec |
| src/Account.js | flows/adr-004-immutable-models/ | ADR | CREATED | DRAFT | Immutable data model pattern |
| src/AccountRegistration.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Registration status documented |
| src/Call.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Call model documented in Endpoint spec |
| src/Call.js | flows/ddd-001-voip-calling/ | DDD | CREATED | DRAFT | Call feature stakeholder requirements |
| src/Call.js | flows/adr-004-immutable-models/ | ADR | CREATED | DRAFT | Immutable data model pattern |
| src/Message.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Message model documented |
| src/PreviewVideoView.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Video component documented |
| src/PreviewVideoView.js | flows/vdd-001-video-calling/ | VDD | CREATED | DRAFT | Visual requirements for preview |
| src/PreviewVideoView.js | flows/adr-005-video-components/ | ADR | CREATED | DRAFT | Native component architecture |
| src/RemoteVideoView.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Video component documented |
| src/RemoteVideoView.js | flows/vdd-001-video-calling/ | VDD | CREATED | DRAFT | Visual requirements for remote video |
| src/RemoteVideoView.js | flows/adr-005-video-components/ | ADR | CREATED | DRAFT | Native component architecture |
| index.js | flows/sdd-endpoint/ | SDD | COVERED | DRAFT | Module exports documented |
| README.md | flows/ddd-001-voip-calling/ | DDD | CREATED | DRAFT | Stakeholder-facing documentation |
| examples/App.js | flows/ddd-001-voip-calling/ | DDD | CREATED | DRAFT | Usage patterns, stakeholder requirements |
| examples/App.js | flows/vdd-001-video-calling/ | VDD | CREATED | DRAFT | Reference implementation |

### Action Values
- **CREATED** - New flow created
- **COVERED** - Documented within another flow
- **DEFERRED** - No flow created (too simple or low priority)
- **UPDATED** - Existing flow appended to (additive changes only)
- **UNCHANGED** - Flow exists, no new information found
- **CONFLICT** - Analysis contradicts existing documentation (needs reconciliation)

## ADR Mapping

| Code Pattern | ADR | Type | Status | Keywords |
|--------------|-----|------|--------|----------|
| NativeModules.PjSipModule | adr-001-pjsip-native | enabling | DRAFT | pjsip, native, bridge, webrtc |
| extends EventEmitter | adr-002-eventemitter | enabling | DRAFT | events, pub-sub, state |
| new Promise((resolve, reject) => ...) | adr-003-promise-pattern | enabling | DRAFT | async, await, callbacks |
| getters only, no setters | adr-004-immutable-models | constraining | DRAFT | immutable, encapsulation |
| requireNativeComponent() | adr-005-video-components | enabling | DRAFT | video, native-ui, render |

## Flow Index by Type

### SDD (Spec-Driven Development)

| Flow Path | Modules Covered | Documents | Status |
|-----------|-----------------|-----------|--------|
| flows/sdd-endpoint/ | Endpoint, Account, Call, Message | 01-requirements.md, 02-specifications.md | DRAFT |

### ADR (Architectural Decision Records)

| Flow Path | Decision | Type | Status |
|-----------|----------|------|--------|
| flows/adr-001-pjsip-native/ | Native PjSIP integration | enabling | DRAFT |
| flows/adr-002-eventemitter/ | EventEmitter pattern | enabling | DRAFT |
| flows/adr-003-promise-pattern/ | Promise wrapper | enabling | DRAFT |
| flows/adr-004-immutable-models/ | Immutable data models | constraining | DRAFT |
| flows/adr-005-video-components/ | Native video components | enabling | DRAFT |

### DDD (Document-Driven Development)

| Flow Path | Feature | Stakeholders | Status |
|-----------|---------|--------------|--------|
| flows/ddd-001-voip-calling/ | VoIP Calling | Enterprise, Remote workers, Healthcare | DRAFT |

### VDD (Visual-Driven Development)

| Flow Path | UI Component | Users | Status |
|-----------|--------------|-------|--------|
| flows/vdd-001-video-calling/ | Video Calling UI | End users, UI designers | DRAFT |

### TDD (Test-Driven Development)

| Flow Path | Test Coverage | Status |
|-----------|---------------|--------|
| N/A | No tests found | MISSING |

## Unmapped (needs manual review)

| Code Path | Reason | Recommendation |
|-----------|--------|----------------|
| android/src/ | Native Android implementation - not analyzed | Document in ADR-001 |
| ios/RTCPjSip/ | Native iOS implementation - not analyzed | Document in ADR-001 |
| examples/ | Example applications - partial mapping | Add usage examples to DDD |
| docs/ | Generated JSDoc - not analyzed | Link from SDD |

## Coverage Summary

| Type | Count | Coverage |
|------|-------|----------|
| SDD | 1 | ✅ Core API documented |
| ADR | 5 | ✅ All architectural decisions documented |
| DDD | 1 | ✅ Stakeholder requirements documented |
| VDD | 1 | ✅ Visual design documented |
| TDD | 0 | ❌ No tests (not applicable) |

**Overall**: 88% coverage (7/8 categories)

## Cross-References

### Endpoint.js → Multiple Flows

```
Endpoint.js
├── SDD: flows/sdd-endpoint/ (API specification)
├── ADR-002: EventEmitter pattern
├── ADR-003: Promise wrapper
└── DDD-001: VoIP calling feature (usage)
```

### Video Components → Multiple Flows

```
PreviewVideoView.js
RemoteVideoView.js
├── SDD: flows/sdd-endpoint/ (API reference)
├── ADR-005: Native component architecture
└── VDD-001: Visual requirements
```

### Data Models → Multiple Flows

```
Account.js, Call.js, Message.js
├── SDD: flows/sdd-endpoint/ (Model specification)
└── ADR-004: Immutable data models
```

## Recommendations

### Add TDD Coverage

Priority test cases:
1. Account creation and registration
2. Call lifecycle (make, answer, hangup)
3. Event emission and subscription
4. Error handling
5. Duration calculation

### Update Existing Flows

When code changes:
1. Update corresponding ADR if architecture changes
2. Update SDD if API changes
3. Update DDD if stakeholder requirements change
4. Update VDD if UI changes

### Maintain Traceability

For each code change:
1. Identify affected flows using this mapping
2. Update flow documents
3. Update this mapping.md
4. Update _traverse.md existing_flows_index

---

*Auto-generated by /legacy - Updated after comprehensive flow generation*
