# Understanding: Messaging

## Phase: EXPLORING

## Hypothesis

Message model is a simple data wrapper for SIP MESSAGE requests, similar to Account and Call models.

## Sources

- `src/Message.js` - Message data model (already read)

## Validated Understanding

> Updated during EXPLORING phase

### Message Model

**Purpose**: Represents a SIP instant message (MESSAGE request).

**Constructor Parameters** (destructuring from object):
- `accountId` - Account ID where message belongs
- `contactUri` - Sender's Contact URI
- `fromUri` - Sender's From URI
- `toUri` - Destination URI
- `body` - Message body content
- `contentType` - MIME type of message body

**URI Parsing Logic**:
```javascript
// Extract name and number from From URI
let match = fromUri.match(/"([^"]+)" <sip:([^@]+)@/);
if (match) {
  fromName = match[1];
  fromNumber = match[2];
} else {
  match = fromUri.match(/sip:([^@]+)@/);
  if (match) fromNumber = match[1];
}
```

**Getters** (8 methods):

**Identification**:
- `getAccountId()` - Account ID (number)

**Sender**:
- `getContactUri()` - Contact URI (string)
- `getFromUri()` - From URI (string)
- `getFromName()` - Sender name (string|null)
- `getFromNumber()` - Sender number (string|null)

**Destination**:
- `getToUri()` - To URI (string)

**Content**:
- `getBody()` - Message body (string|null)
- `getContentType()` - MIME type (string|null)

**Characteristics**:
- Immutable (no setters)
- URI parsing in constructor
- Pure data accessor pattern
- Similar structure to Call model (smaller)

## Children Identified

> Populated during SPAWNING phase

[No children - Message is a simple data model]

## Dependencies

- **Uses**: None (pure data model)
- **Used by**: Endpoint (message_received event), application code

## Key Insights

1. **Consistent Pattern**: Follows same immutable data model pattern as Account/Call
2. **URI Parsing**: Reuses same regex pattern as Call for extracting name/number
3. **Minimal API**: Only 8 getters - much simpler than Call (30+) or Account (15+)
4. **MIME Support**: contentType allows text/plain, text/xml, etc.
5. **Event-Driven**: Messages arrive via message_received event from Endpoint

## ADR Candidates

- **SIP MESSAGE Support**: Instant messaging via SIP vs separate messaging protocol

## Flow Recommendation

- **Type**: SDD (already covered in flows/sdd-endpoint/)
- **Confidence**: high
- **Rationale**: Message model is part of Endpoint API specification

## Synthesis

> Updated during SYNTHESIZING phase

### Combined Understanding

**Message** is a simple immutable data model for SIP instant messages:

- **8 getters** for message metadata
- **URI parsing** for sender name/number extraction
- **MIME support** via contentType property
- **Event-driven** delivery via message_received event

**No separate flow needed** - documented in flows/sdd-endpoint/

## Bubble Up

> Summary to pass to parent during EXITING

- Message: Simple message data model (8 getters)
- URI parsing for sender extraction
- Already documented in flows/sdd-endpoint/

---

*Phase: EXPLORING | Depth: 1 | Parent: root*
