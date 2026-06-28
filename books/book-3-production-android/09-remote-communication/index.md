# DOC 9 — Remote Communication

**Purpose:** Understand how data travels from another computer into your Android application.

---

## Part 1 — Computer Communication Foundations

**Purpose:** Understand how two computers communicate across a network before introducing Android-specific technologies.

### Concepts

- [ ] Why Networks Exist
- [ ] Client & Server
- [ ] IP Address
- [ ] Domain Name
- [ ] DNS
- [ ] Port
- [ ] Socket (Conceptual)
- [ ] Connection

---

## Part 2 — HTTP

**Purpose:** Understand the language computers use to communicate over the web.

### Concepts

- [ ] Why HTTP Exists
- [ ] HTTP
- [ ] URL
- [ ] Request
- [ ] Response

- [ ] HTTP Methods
    - [ ] GET
    - [ ] POST
    - [ ] PUT
    - [ ] PATCH
    - [ ] DELETE

- [ ] Headers
- [ ] Body
- [ ] Status Codes
- [ ] HTTPS

---

## Part 3 — APIs & Web Services

**Purpose:** Understand how applications expose functionality over HTTP.

### Concepts

- [ ] Why APIs Exist
- [ ] API
- [ ] REST
- [ ] RESTful APIs
- [ ] Endpoint

- [ ] Request Parameters
    - [ ] Path Parameters
    - [ ] Query Parameters

- [ ] Authentication
- [ ] Authorization
- [ ] Tokens

---

## Part 4 — Data Representation

**Purpose:** Understand how applications exchange structured data.

### Concepts

- [ ] Why Serialization Exists
- [ ] Serialization
- [ ] Deserialization
- [ ] JSON
- [ ] DTO
- [ ] API Versioning
- [ ] Backward Compatibility

---

## Part 5 — Android Networking

**Purpose:** Learn how Android applications perform network requests.

### Concepts

- [ ] Why Networking Libraries Exist
- [ ] OkHttp
- [ ] Retrofit
- [ ] Converter Factories
- [ ] Interceptors
- [ ] Timeouts
- [ ] Error Handling

---

## Part 6 — Production Networking Patterns

**Purpose:** Learn how production Android applications organize network communication.

### Concepts

- [ ] API Service Layer
- [ ] Remote Data Source
- [ ] Network Error Modeling
- [ ] Retry Strategies
- [ ] Logging Interceptors
- [ ] API Design Best Practices

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
