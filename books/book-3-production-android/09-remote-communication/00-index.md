# DOC 9 — Remote Communication

**Purpose:** Understand how data travels from another computer into your Android application.

---

## Part 1 — Computer Communication Foundations

**Purpose:** Understand how two computers discover and communicate with each other before introducing web technologies.

### Concepts

- [x] 01. Why Networks Exist
- [x] 02. Client & Server
- [x] 03. IP Address
- [x] 04. Domain Name
- [x] 05. DNS
- [x] 06. Port
- [x] 07. Socket (Conceptual)
- [x] 08. Connection

---

## Part 2 — HTTP

**Purpose:** Understand the language clients and servers use to communicate over the web.

### Concepts

- [x] 01. HTTP
- [x] 02. Request
- [x] 03. Response

- [x] 04. HTTP Methods
- [x] 05. GET
- [x] 06. POST
- [x] 07. PUT vs PATCH
- [x] 08. DELETE

- [x] 09. URL
- [x] 10. Path Parameters
- [x] 11. Query Parameters

- [x] 12. Headers
- [x] 13. Body

- [x] 14. HTTP Status Codes
- [x] 15. Status Code Categories
- [x] 16. 2xx Success
- [x] 17. 4xx Client Errors
- [x] 18. 5xx Server Errors

- [x] 19. HTTPS

---

## Part 3 — APIs & Web Services

**Purpose:** Understand how applications expose functionality over HTTP.

### Concepts

- [x] 01. API
- [x] 02. Web Service
- [x] 03. Endpoint
- [x] 04. REST & RESTful APIs
- [x] 05. Authentication vs Authorization
- [x] 06. Tokens

---

# Part 4 — Data Representation

**Purpose:** Understand how applications exchange structured data across a network.

## Concepts

- [x] 01. Serialization & Deserialization
- [x] 02. JSON
- [x] 03. DTO
- [x] 04. API Versioning & Backward Compatibility

---

# Part 5 — Android Networking

**Purpose:** Understand the networking stack used by production Android applications.

## Concepts

- [x] 01. OkHttp
- [x] 02. Retrofit
- [x] 03. Converter Factories
- [x] 04. Interceptors
- [x] 05. Timeouts
- [x] 06. Error Handling

---

# Part 6 — Production Networking Patterns

**Purpose:** Learn how production Android applications organize network communication.

## Concepts

- [x] 01. API Service Layer
- [x] 02. Remote Data Source
- [ ] 03. Production Error Modeling
- [ ] 04. Retry Strategies
- [ ] 05. API Design Best Practices

---

# One Remaining Question

We've learned how Android applications communicate with remote servers,

how they exchange requests and responses,

and how they securely identify users.

A natural question appears.

Once data arrives inside the application,

where should it be stored?

Some data is needed only for a few seconds.

Some should survive app restarts.

Some should even remain available without an internet connection.

That leads us to the next document.

**DOC 10 — Local Persistence**
