# 🔐 SecureVault — Spring Boot 3 JWT Authentication & Authorization

> A hands-on learning project implementing stateless JWT-based authentication and role-based authorization using Spring Boot 3 and Spring Security 6.

---

## 🧭 About This Project

This project was built as a **deep-dive learning exercise** into modern backend security. Rather than relying on third-party auth services, I implemented the full authentication pipeline from scratch — from password hashing and token generation to request filtering and access control — to truly understand what happens under the hood when a user logs in.

The project is based on real-world security patterns and serves as my personal reference implementation for JWT-based auth in the Spring ecosystem.

---

## 🧠 What I Learned

Building this project gave me hands-on experience with some of the most important concepts in backend security. Here's a breakdown of the key things I picked up:

### 🔑 JSON Web Tokens (JWT)
- How JWTs are structured: **Header**, **Payload**, and **Signature**
- How to sign tokens using a secret key with the **HS256** algorithm
- How to extract claims (like username and expiry) from a token
- The difference between **access tokens** (short-lived) and **refresh tokens** (long-lived)
- How to validate a token on every incoming request without hitting the database for session data

### 🛡️ Spring Security 6 Architecture
- How the **Security Filter Chain** works and how requests flow through it
- How to replace the deprecated `WebSecurityConfigurerAdapter` with the new **bean-based configuration** in Spring Security 6
- How to configure **stateless session management** — the server holds no session state; the JWT carries everything
- How to register a custom `OncePerRequestFilter` (`JwtAuthFilter`) to intercept and validate tokens before reaching controllers
- How `SecurityContextHolder` works and how to manually set authentication inside it

### 🔒 Password Security
- Why storing plain-text passwords is never acceptable
- How **BCrypt** works: salted, slow-by-design hashing that resists brute-force attacks
- How Spring's `PasswordEncoder` integrates seamlessly with `UserDetailsService`

### 👤 UserDetails & Custom User Entity
- How to implement `UserDetails` directly on a JPA `@Entity` to bridge Spring Security and the database layer
- How Spring uses `UserDetailsService` to load a user by username during authentication
- Mapping user **roles** (e.g., `ROLE_USER`, `ROLE_ADMIN`) to Spring Security's `GrantedAuthority`

### 🚦 Role-Based Authorization
- How to restrict endpoint access by role using `@PreAuthorize` and `HttpSecurity` config
- How Spring Security evaluates authorities to allow or deny requests
- How to return meaningful **403 Forbidden** and **401 Unauthorized** responses via a custom `AccessDeniedHandler` and `AuthenticationEntryPoint`

### 🔄 Refresh Token Flow
- Why access tokens must be short-lived (and why that creates UX problems)
- How refresh tokens solve this: persisted server-side, used only to issue new access tokens
- How to store refresh tokens in the database and invalidate old ones on re-use or logout

### 🗃️ Spring Data JPA
- Using `JpaRepository` to keep data access clean and minimal
- Writing custom query methods like `findByEmail()` and `findByToken()`
- How Hibernate auto-generates schema from entity annotations (`@Entity`, `@Column`, `@Enumerated`)

### 📦 Clean Architecture & Separation of Concerns
- Organising code into `auth`, `config`, `token`, and `user` packages by responsibility
- Keeping business logic in **service classes**, not controllers
- Using **DTOs** (`AuthenticationRequest`, `RegisterRequest`, `AuthenticationResponse`) to decouple API contracts from internal models

### 🐳 Docker & Docker Compose
- Running a **PostgreSQL** database inside a Docker container during development
- Writing a `docker-compose.yml` to spin up infrastructure with a single command
- Understanding how Spring connects to a containerised database via JDBC URL

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| Java 17 | Core language |
| Spring Boot 3 | Application framework |
| Spring Security 6 | Authentication & authorization |
| JWT (io.jsonwebtoken) | Token generation & validation |
| BCrypt | Password hashing |
| Spring Data JPA | Database access layer |
| PostgreSQL | Relational database |
| Docker / Docker Compose | Local infrastructure |
| Maven | Build & dependency management |

---

## 🚀 Running the Project

### Prerequisites
- JDK 17+
- Maven 3+
- Docker (for the database)

### Steps

1. **Clone the repo**
   ```bash
   git clone https://github.com/saMM7111/SecureVault-Spring-Boot-3-JWT-Authentication-Authorization.git
   cd SecureVault-Spring-Boot-3-JWT-Authentication-Authorization
   ```

2. **Start the database**
   ```bash
   docker-compose up -d
   ```

3. **Build and run**
   ```bash
   mvn clean install
   mvn spring-boot:run
   ```

App runs at: `http://localhost:8080`

---

## 📡 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/v1/auth/register` | Register a new user |
| `POST` | `/api/v1/auth/authenticate` | Login and receive JWT + refresh token |
| `POST` | `/api/v1/auth/refresh-token` | Get a new access token using a refresh token |
| `GET` | `/api/v1/demo-controller` | Protected endpoint (requires valid JWT) |

---

## 🔁 Authentication Flow

```
1. User registers  →  password BCrypt-hashed  →  saved to DB
2. User logs in    →  credentials verified    →  JWT + refresh token returned
3. User requests   →  JWT extracted from header → validated by JwtAuthFilter
4. Token expires   →  refresh token sent      →  new JWT issued
5. User logs out   →  refresh token invalidated in DB
```

---

## 💡 Key Takeaways

- **Security is not magic** — Spring Security is a chain of filters and each one has a clear, debuggable purpose.
- **Stateless auth scales** — because no session is stored server-side, the same logic works across multiple instances.
- **JWT is a contract** — the server trusts the token's signature, not a session store, which means the secret key must be protected.
- **BCrypt is intentionally slow** — and that is the entire point. Speed is the enemy of password security.
- **Refresh tokens are a trade-off** — they improve UX but introduce server-side state, which is worth understanding explicitly.

---

## 📚 Reference

This project was built by following along with and extending the work of [Ali Bouali](https://github.com/ali-bouali/spring-boot-3-jwt-security). All implementation here represents my own understanding and working code.

---

## 📄 License

This project is open source and available under the [Apache License 2.0](LICENSE).
