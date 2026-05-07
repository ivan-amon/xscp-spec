# Encoding and Data Format

XSCP is a text-based protocol that enforces a strict structure to ensure predictable parsing and high performance.

## Encoding
All data exchanged via XSCP must be encoded in **UTF-8**. This ensures universal compatibility across different operating systems and programming languages while maintaining a simple byte-to-character relationship for ASCII-range characters.

## Framing and Delimiters
The protocol is **line-oriented**, meaning each PDU *(Protocol Data Unit)* is treated as a single, discrete line of text.

* **PDU Terminator:** Every message must end with the **CRLF** sequence (`\r\n`). Parsers should treat the arrival of `\r\n` as the signal to process the preceding buffer.
* **Field Separator:** Within a PDU, fields are separated by the **pipe** character (`|`). 
* **Structure:** A typical PDU follows the pattern: `FIELD1|FIELD2|PAYLOAD\r\n`.

## Formatting Constraints
To ensure integrity and prevent protocol smuggling, the following rules apply:

* **Control Fields:** The pipe character (`|`) and CRLF sequence are **strictly forbidden** within control fields (such as the *source* or *command* identifiers).
* **Payload Flexibility:** The pipe character (`|`) **is allowed** within the message payload section. The parser handles this by only splitting the PDU into a fixed number of structural segments, treating the remainder as the raw payload.
* **Line Breaks:** The CRLF sequence remains globally forbidden within any part of the PDU content to avoid premature message termination.