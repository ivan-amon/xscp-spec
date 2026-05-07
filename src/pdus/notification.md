# Notification PDUs

A **Notification** is an unsolicited message pushed from the Server to a Client (or relayed between Clients via the Server) to inform about an event. Unlike a Request, a Notification does **not** require a Response.

## Wire Format

XSCP notifications are structured as a single line of text with a strict field-based layout:

```text
+---------------------------------------------------------------------------+
|   Notification Type (4 Bytes)   |   Source (Min 3 Bytes, Max 32 Bytes)    |
|---------------------------------------------------------------------------|
|                 Message (Max 472 Bytes) + \r\n (2 Bytes)                  |
+---------------------------------------------------------------------------+
```

## Field Specification

### 1. Notification Type

The Notification Type is a fixed-length functional identifier.

- **Type:** 4-character ASCII string (Uppercase).
- **Length:** Exactly 4 bytes.
- **Function:** It acts as the instruction for the client's parser to route the notification to the correct logic handler.
- **Valid Values:** `BRDC` (Broadcast).

### 2. Source (Origin Identity)

The **Source** field identifies who originated the notification.

- **Type:** UTF-8 string.
- **Length:** Variable (Minimum 3 bytes, Maximum 32 bytes).
- **Constraints:**
  - **Prohibited Characters:** Must not contain the pipe (`|`) character, as it would break the field segmentation.
  - **Prohibited Sequences:** Must not contain Carriage Return (`\r`) or Line Feed (`\n`).
- **Usage:** Either a hostname or nickname of a client (for `BRDC` notifications), or the reserved string `XSCP_SERVER` when the notification is emitted by the server itself.

### 3. Message (Payload)

The **Message** field contains the data carried by the notification.

- **Type:** UTF-8 string.
- **Length:** Variable (Maximum 472 bytes).
- **Parsing Logic:** The parser reads everything from the second pipe until the `\r\n` terminator.
- **Constraints:**
  - **Allowed Characters:** The pipe (`|`) **is permitted here**, allowing for nested data or complex text.
  - **Prohibited Sequences:** Must not contain `\r\n` to prevent "PDU Smuggling" (injecting a second notification into the same stream).

## Validation Logic (Client-Side)

When a client receives a Notification, it must validate the fields in this specific order:

1. **Framing Check:** Verify the string ends with `\r\n`. If not, discard or wait for more data.
2. **Segmentation:** Split the string using the first two pipes.
3. **Notification Type Validation:** Check if the first 4 bytes match a known notification type. If not, return an error.
4. **Source Integrity:** Check that the second segment is between 3 and 32 bytes and contains no forbidden characters.
5. **Size Compliance:** Ensure the total PDU does not exceed 512 bytes.
