# Understanding: Account Management

## Phase: ENTERING

## Hypothesis

Account management handles SIP account lifecycle:
1. **Account Model**: Data wrapper for account configuration (username, domain, password, proxy, etc.)
2. **Registration Model**: Tracks registration status (active/inactive, status codes, reason)
3. **Operations**: Create, register, delete accounts via Endpoint

Expected:
- Simple data models with getters
- No internal state mutation
- Registration status polling or event-driven updates

## Sources

- `src/Account.js` - Account data model (already read)
- `src/AccountRegistration.js` - Registration status model (already read)

## Validated Understanding

> Updated during EXPLORING phase

### Account Model

**Purpose**: Data wrapper for SIP account configuration and state.

**Properties** (all via getters, no direct access):
- `_data`: Raw account data from native module
- `_registration`: AccountRegistration instance

**Getters** (15 total):
1. `getId()` - Account ID (number)
2. `getURI()` - SIP URI for registration (e.g., "sip:serviceprovider")
3. `getName()` - Full name
4. `getUsername()` - Username
5. `getDomain()` - Domain (number|null)
6. `getPassword()` - Password
7. `getProxy()` - Proxy server
8. `getTransport()` - Transport protocol
9. `getContactParams()` - Contact header parameters
10. `getContactUriParams()` - Contact URI parameters
11. `getRegServer()` - Registration server
12. `getRegTimeout()` - Registration timeout
13. `getRegContactParams()` - Registration contact parameters
14. `getRegHeaders()` - Registration headers (Object)
15. `getRegistration()` - AccountRegistration instance

**Key Characteristics**:
- Constructor receives raw data object
- No setters (immutable after creation)
- No methods that modify state
- Pure data accessor pattern

### AccountRegistration Model

**Purpose**: Track SIP registration status.

**Properties**:
- `_status`: SIP status code (string|null) - empty means not registered
- `_statusText`: Human-readable status description
- `_active`: Boolean - true if actively registered
- `_reason`: Reason phrase from server

**Methods**:
- `getStatus()` - SIP status code
- `getStatusText()` - Status description
- `isActive()` - Registration active flag
- `getReason()` - Server reason phrase
- `toJson()` - Serialize to plain object

**Key Characteristics**:
- Constructor receives status object
- No setters (immutable snapshot)
- `toJson()` for serialization

### Relationship

```
Endpoint
  ├── creates Account (via createAccount)
  ├── emits Account (via registration_changed event)
  └── Account.getRegistration() → AccountRegistration
```

## Children Identified

> Populated during SPAWNING phase

| Child | Hypothesis | Status |
|-------|------------|--------|
| account-model | Account data structure and getters | PENDING |
| registration-model | Registration status tracking | PENDING |

[No deeper concepts - these are simple data models]

## Dependencies

- **Uses**: None (pure data models)
- **Used by**: Endpoint (account operations), application code

## Key Insights

1. **Immutable Data Pattern**: Both models are immutable - no setters, only getters
2. **Composition**: Account contains AccountRegistration (has-a relationship)
3. **Serialization Support**: AccountRegistration has `toJson()` for easy serialization
4. **Type Hints in JSDoc**: All getters have `@returns` annotations
5. **No Business Logic**: Pure data containers, all logic in Endpoint

## ADR Candidates

- **Immutable Data Models**: Choice to use getters-only pattern vs mutable properties
- **Composition over Inheritance**: AccountRegistration is separate, not inherited

## Flow Recommendation

- **Type**: SDD (already covered in flows/sdd-endpoint/)
- **Confidence**: high
- **Rationale**: These models are part of Endpoint API - no separate flow needed

## Synthesis

> Updated during SYNTHESIZING phase

### Combined Understanding

Account Management consists of two **immutable data models**:

1. **Account**: 15 getters for SIP account configuration
   - Created by Endpoint.createAccount()
   - Emitted via registration_changed events
   - Contains AccountRegistration instance

2. **AccountRegistration**: 4 properties for registration status
   - SIP status codes (200, 401, 403, etc.)
   - Active/inactive flag
   - Server reason phrase
   - Serializable via toJson()

**No separate flow needed** - these are integral parts of the Endpoint SDD already created.

## Bubble Up

> Summary to pass to parent during EXITING

- Account: Configuration data wrapper with 15+ getters
- AccountRegistration: Status tracking (4 properties)
- Both are passive models, no business logic
- Already documented in flows/sdd-endpoint/

---

*Phase: ENTERING | Depth: 1 | Parent: root*
