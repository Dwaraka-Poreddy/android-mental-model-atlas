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
- [ ] 17. 4xx Client Errors
- [ ] 18. 5xx Server Errors

- [ ] 19. HTTPS

---

## Part 3 — APIs & Web Services

**Purpose:** Understand how applications expose functionality over HTTP.

### Concepts

- [ ] 01. Why APIs Exist
- [ ] 02. API
- [ ] 03. Endpoint
- [ ] 04. REST
- [ ] 05. RESTful APIs

- [ ] 06. Authentication vs Authorization
- [ ] 07. Tokens

---

## Part 4 — Data Representation

**Purpose:** Understand how applications exchange structured data.

### Concepts

- [ ] 01. Why Serialization Exists
- [ ] 02. Serialization
- [ ] 03. Deserialization
- [ ] 04. JSON
- [ ] 05. DTO
- [ ] 06. API Versioning
- [ ] 07. Backward Compatibility

---

## Part 5 — Android Networking

**Purpose:** Learn how Android applications perform network requests.

### Concepts

- [ ] 01. Why Networking Libraries Exist
- [ ] 02. OkHttp
- [ ] 03. Retrofit
- [ ] 04. Converter Factories
- [ ] 05. Interceptors
- [ ] 06. Timeouts
- [ ] 07. Error Handling

---

## Part 6 — Production Networking Patterns

**Purpose:** Learn how production Android applications organize network communication.

### Concepts

- [ ] 01. API Service Layer
- [ ] 02. Remote Data Source
- [ ] 03. Network Error Modeling
- [ ] 04. Retry Strategies
- [ ] 05. Logging Interceptors
- [ ] 06. API Design Best Practices

---

## One Remaining Question

We've learned how data travels from a remote server into our application.

A natural question appears.

> **Once we've downloaded the data, where should we keep it?**

Some information only lives for a few seconds.

Some needs to survive app restarts.

Some must even work without an internet connection.

That leads us to the next document.

**DOC 10 — Local Persistence**
