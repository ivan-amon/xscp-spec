# Transport Layer

XSCP operates directly over **TCP** (Transmission Control Protocol). It relies entirely on TCP's built-in mechanisms to guarantee the reliable, ordered, and error-checked delivery of the data stream. 

The protocol does not implement its own packet reordering or retransmission logic, delegating these responsibilities to the underlying transport layer.

* **Underlying Protocol:** TCP
* **Default Port:** `7878`