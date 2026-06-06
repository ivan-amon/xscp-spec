# Response PDUs

A **Response** is a message sent from a Server to a Client as the mandatory reply to a previously received **Request**. It reports the outcome of the operation in a compact, two-field format.

## Wire Format

XSCP responses are structured as a single line of text with a strict field-based layout:

```text
+-----------------------------------------------------------------------+
|   Status Code (1-3 ASCII digits)   |   Reason Phrase (Max 32 Bytes)   |
+-----------------------------------------------------------------------+
```

The two fields are delimited by a single pipe (`|`) and the line is terminated by `\r\n`. The total PDU size must not exceed **38 bytes** (delimiter and CRLF included).

## Field Specification

### 1. Status Code

The Status Code is a numeric identifier indicating the outcome of the request.

- **Type:** ASCII numeric string.
- **Length:** Variable (1 to 3 digits).
- **Range:** `0` – `599`.
- **Function:** It allows the client to programmatically determine whether the request succeeded, failed, or requires further action, without having to interpret human-readable text.

#### Available status codes:

- `200`: The XSCP Request was processed successfully.
- `400`: Bad XSCP Request.
- `401`: Invalid Credentials.
- `402`: Host exceeded auth attempts.
- `500`: Internal XSCP server error. 



### 2. Reason Phrase

The **Reason Phrase** is a short, human-readable description that complements the status code.

- **Type:** UTF-8 string.
- **Length:** Variable (Maximum 32 bytes).
- **Constraints:**
  - **Prohibited Characters:** Must not contain the pipe (`|`) character, as it would break the field segmentation.
  - **Prohibited Sequences:** Must not contain Carriage Return (`\r`) or Line Feed (`\n`), to prevent "PDU Smuggling" (injecting a second response into the same stream).
- **Usage:** Intended for logging, debugging or display purposes. The reason phrase **should** be in **Title Case**. Clients **must not** rely on its exact content to drive logic — that is the role of the Status Code.



## Validation Logic (Client-Side)

When a client receives a Response, it must validate the fields in this specific order:

1. **Framing Check:** Verify the string ends with `\r\n`. If not, discard or wait for more data.
2. **Segmentation:** Split the string using a single pipe. The result must contain exactly two segments.
3. **Status Code Validation:** Verify that the first segment parses as a number and does not exceed `599`.
4. **Reason Phrase Integrity:** Check that the second segment is at most 32 bytes and contains no forbidden characters.
5. **Size Compliance:** Ensure the total PDU does not exceed 38 bytes.
