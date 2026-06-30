# DOC 9 — Remote Communication

**Purpose:** How data enters the application

This document teaches how Android applications communicate with remote servers. It covers the complete journey from network fundamentals through HTTP, APIs, data representation, and Android-specific networking libraries.

## Section Structure

- [x] [01-computer-communication-foundations](./01-computer-communication-foundations/) — Network fundamentals
- [x] [02-http](./02-http/) — The HTTP protocol
- [x] [03-apis-and-web-services](./03-apis-and-web-services/) — API design
- [x] [04-data-representation](./04-data-representation/) — How data is serialized
- [x] [05-android-networking](./05-android-networking/) — Android networking libraries
- [x] [06-production-networking-patterns](./06-production-networking-patterns/) — Production patterns

## Reading Order

Read sections in the order listed because each builds on the previous:

1. **Network fundamentals** (how computers talk to each other)
2. **HTTP protocol** (how the web works)
3. **APIs and web services** (how applications expose data)
4. **Data representation** (how data is serialized/deserialized)
5. **Android networking** (libraries for making requests)
6. **Production patterns** (how real apps do it)

## How This Fits Into The Atlas

This document answers: **How do Android applications get data from remote servers?**

It's the first step in understanding production architecture. Once you understand remote communication, you can build repositories and complete data flows.

Next comes **DOC 10 — Local Persistence**, which teaches how data is stored locally.

---

**Start with:** [01-computer-communication-foundations](./01-computer-communication-foundations/)
