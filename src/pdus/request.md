# Request PDUs

A **Request** is a message sent from a Client to a Server that triggers a specific action and requires a mandatory **Response**. 

## Wire Format

XSCP requests are structured as a single line of text with a strict field-based layout:

```text
+------------------------------------------------------------------+
|   OPCODE (4 Bytes)   |    Source (Min 3 Bytes, Max 32 Bytes)     |
|------------------------------------------------------------------|
|          Message (Max 472 Bytes) + \r\n (2 Bytes)                |
+------------------------------------------------------------------+
```

## Field Specification

### 1. OPCODE (Operation Code)

The OPCODE is a fixed-length functional identifier.

- **Type:** 4-character ASCII string (Uppercase).
- **Length:** Exactly 4 bytes.
- **Function:** It acts as the instruction for the server's parser to route the request to the correct logic handler.
- **Valid Values:** `LOGN` (Login), `SEND` (Message), `EXIT` (Disconnect).

### 2. Source (Sender Identity)

The **Source** field identifies who is making the request.

- **Type:** UTF-8 string.
- **Length:** Variable (Minimum 3 bytes, Maximum 32 bytes).
- **Constraints:**
  - **Prohibited Characters:** Must not contain the pipe (`|`) character, as it would break the field segmentation.
  - **Prohibited Sequences:** Must not contain Carriage Return (`\r`) or Line Feed (`\n`).
- **Usage:** Typically used for usernames or session IDs.

### 3. Message (Payload)

The **Message** field contains the data intended for the command's execution.

- **Type:** UTF-8 string.
- **Length:** Variable (Maximum 472 bytes).
- **Parsing Logic:** The parser reads everything from the second pipe until the `\r\n` terminator.
- **Constraints:**
  - **Allowed Characters:** The pipe (`|`) **is permitted here**, allowing for nested data or complex text.
  - **Prohibited Sequences:** Must not contain `\r\n` to prevent "PDU Smuggling" (injecting a second command into the same stream).

## Validation Logic (Server-Side)

When a server receives a Request, it must validate the fields in this specific order:

1. **Framing Check:** Verify the string ends with `\r\n`. If not, discard or wait for more data.
2. **Segmentation:** Split the string using the first two pipes.
3. **OPCODE Validation:** Check if the first 4 bytes match a known command. If not, return an error.
4. **Source Integrity:** Check that the second segment is between 3 and 32 bytes and contains no forbidden characters.
5. **Size Compliance:** Ensure the total PDU does not exceed 512 bytes.

