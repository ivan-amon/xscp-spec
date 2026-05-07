# XSCP Specification

<p align="center">
  <img src="img/logo.svg" alt="XSCP Logo" width="250px">
</p>

**XSCP** (XSCP Stream Communication Protocol) is a text-based communication protocol.

This specification is designed and maintained by [Iván Amón](https://ivanamon.dev), an Engineering student at UPM. It focuses on efficiency and the safety principles of the **Rust** programming language.

## Ecosystem

XSCP is fundamentally **language-agnostic**. Because it relies strictly on standard TCP streams and UTF-8 text, it can easily be implemented in any programming language. 

However, its native and reference ecosystem is built in **Rust**. The protocol's design heavily reflects Rust's core philosophies: **memory safety, zero-allocation parsing, and bare-metal performance**. By enforcing strict PDU sizes and avoiding complex nested structures, the Rust implementation guarantees safety against common network vulnerabilities (such as buffer overflows or memory leaks) while maintaining an incredibly low footprint.

## Primary Use Cases

XSCP is a **general-purpose** communication protocol. While it does not impose restrictions on the type of data being exchanged (as long as it fits the UTF-8 text format), its design choices make it particularly efficient for:

* **Lightweight Messaging:** Such as chat rooms or notification systems, thanks to its native support for asynchronous notifications and line-oriented parsing.
* **Microcontrollers and Embedded Systems:** Ideal for devices like the **ESP32** due to its low memory footprint and fixed PDU limits, allowing for pre-allocated buffers.
* **General Command & Control:** Sending instructions to remote services without the overhead of heavier protocols like HTTP.

## Project Resources
- **Source Code**: [GitHub](https://github.com/ivan-amon/xscp).
- **Rust Crate:** Integrate XSCP into your project via [Crates.io](https://crates.io/crates/xscp).