# Atlas Roadmap

This document contains books that are intentionally outside the current scope of the Atlas.

These are not promises.

They are potential future expansions discovered while building the Atlas.

---

## Future Book — Authentication & Security

**Purpose:** Understand how modern applications securely identify users, manage sessions, and protect resources.

### Part 1 — Identity

- Authentication Recap
- Authorization Recap
- Identity
- Credentials

### Part 2 — Sessions & Tokens

- Session
- Session ID
- Stateless Authentication
- Tokens
- Opaque Tokens
- JWT (JSON Web Token)
- JWT Structure
    - Header
    - Payload
    - Signature
- JWT Verification
- Token Expiration
- Token Revocation

### Part 3 — OAuth & OpenID Connect

- Why OAuth Exists
- OAuth
- OAuth Roles
- Authorization Code Flow
- PKCE
- OpenID Connect
- ID Token
- Access Token
- Refresh Token

### Part 4 — Production Authentication

- Login Flow
- Logout Flow
- Token Refresh
- Secure Token Storage
- Token Rotation
- Single Sign-On (SSO)

### Part 5 — Web Security

- HTTPS Revisited
- Cookies
- CSRF
- CORS
- XSS
- Certificate Pinning
- API Keys
- Secrets Management

### Part 6 — Android Security

- Android Keystore
- EncryptedSharedPreferences
- Biometric Authentication
- Secure Credential Storage
- Network Security Config

---
## Future Book — Android Architecture

**Purpose:** Learn how production Android applications are organized into clean, maintainable, and scalable architectures.

### Part 1 — Why Architecture Exists

- Why Architecture Exists
- Separation of Concerns
- Coupling & Cohesion
- Layered Architecture

### Part 2 — MVVM

- Why MVVM Exists
- Model
- View
- ViewModel
- State Management

### Part 3 — Repository Pattern

- Why Repository Exists
- Repository
- Multiple Data Sources
- Repository Responsibilities

### Part 4 — Data Layer

- Remote Data Source
- Local Data Source
- Cache
- Offline First
- Synchronization

### Part 5 — Domain Layer

- Use Cases
- Business Logic
- Domain Models
- Dependency Rule

### Part 6 — Dependency Injection

- Why Dependency Injection Exists
- Manual Dependency Injection
- Service Locator
- Hilt
- Scopes
- Modules

### Part 7 — Clean Architecture

- Clean Architecture
- Layer Communication
- Dependency Rule
- Feature Architecture
- Putting Everything Together
