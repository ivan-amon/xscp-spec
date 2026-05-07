# 3. PDU Definitions

This chapter defines the structure and semantics of the **Protocol Data Units (PDUs)** used in XSCP.
Each PDU is designed to be parsed with minimal overhead, adhering to strict size constraints and a predictable field-based layout.

## PDU Categories

XSCP classifies communication into three distinct types:

1.  **Request:** A message sent from a client to a server requiring a mandatory response.
2.  **Response:** A mandatory reply to a Request.
3.  **Notification:** An asynchronous, one-way message that does not require a reply.

## Size Constraints (Network Budget)

To ensure compatibility with low-memory devices and maintain high performance, XSCP enforces strict byte limits:

* **Requests & Notifications:** Maximum of **512 bytes**.
* **Responses:** Maximum of **36 bytes** (designed for status codes and brief metadata).