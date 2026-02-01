---

# 📘 BACKEND DESIGN DOCUMENT

**Academic Management Portal – Java Backend**

**Primary Goals**

* Predictable structure
* Frontend-first API contracts
* Role-based security
* Scale-ready (but not over-engineered)

---

## 0️⃣ GLOBAL BACKEND RULES (NON-NEGOTIABLE)

1. **One domain = one package**
2. **Controllers never contain business logic**
3. **Services never return entities**
4. **Repositories never know about DTOs**
5. **Frontend never assembles business rules**
6. **All APIs are versioned**
7. **All responses are JSON**
8. **All dates are ISO-8601 UTC**

---

## 1️⃣ HIGH-LEVEL BACKEND STRUCTURE

```
src/main/java/com/college/portal/
│
├── auth/
├── users/
├── attendance/
├── courses/
├── grades/
├── fees/
├── notices/
│
├── common/
│   ├── config/
│   ├── security/
│   ├── exception/
│   ├── response/
│   └── utils/
│
└── Application.java
```

> **Rule**
> Backend domains must **mirror frontend features/**

---

## 2️⃣ DOMAIN PACKAGE STRUCTURE (STANDARD TEMPLATE)

Each domain **must** follow this exact structure.

```
domain-name/
├── controller/
├── service/
├── repository/
├── entity/
├── dto/
├── mapper/
└── validation/
```

This uniformity is intentional.
A coding agent should **never guess where logic lives**.

---

## 3️⃣ FILE-LEVEL RESPONSIBILITIES (IMPORTANT)

Below is the **canonical meaning** of each layer.

---

## `controller/` — HTTP Layer

### Example

`AttendanceController.java`

**Responsibilities**

* Map HTTP routes
* Accept request DTOs
* Return response DTOs
* Apply validation annotations

**Must**

* Call service methods
* Use `@RestController`
* Use `@RequestMapping("/api/v1/...")`

**Must NOT**

* Contain business rules
* Access repositories
* Convert entities

---

### Example Method Pattern

```java
@PostMapping("/submit")
public ApiResponse<AttendanceResponse> submitAttendance(
    @Valid @RequestBody AttendanceRequest request
)
```

---

## `service/` — Business Logic Layer

### Example

`AttendanceService.java`

**Responsibilities**

* Apply business rules
* Enforce role permissions
* Coordinate repositories
* Call mappers

**Must**

* Be stateless
* Throw domain exceptions

**Must NOT**

* Handle HTTP concepts
* Return entities
* Catch exceptions unnecessarily

---

### Example Responsibilities

* Check if attendance already exists
* Validate faculty permission
* Compute attendance percentage

---

## `repository/` — Persistence Layer

### Example

`AttendanceRepository.java`

**Responsibilities**

* DB access only
* Query definitions

**Must**

* Extend `JpaRepository`
* Use method naming or JPQL

**Must NOT**

* Contain business logic
* Know about DTOs

---

## `entity/` — Database Model

### Example

`Attendance.java`

**Responsibilities**

* Represent DB tables
* Define relations

**Must**

* Use JPA annotations
* Be persistence-only

**Must NOT**

* Contain business logic
* Contain API annotations

---

## `dto/` — API Contract Layer

### Types

* Request DTOs
* Response DTOs

### Example Files

```
AttendanceRequest.java
AttendanceResponse.java
AttendanceSummaryResponse.java
```

**Responsibilities**

* Define API input/output
* Be frontend-friendly
* Be stable

**Must**

* Be immutable if possible
* Avoid nested chaos

**Must NOT**

* Contain entity references
* Contain persistence annotations

---

## `mapper/` — Conversion Layer

### Example

`AttendanceMapper.java`

**Responsibilities**

* Convert Entity ↔ DTO
* Centralize transformation logic

**Must**

* Be pure
* Be deterministic

**Must NOT**

* Call repositories
* Apply business rules

---

## `validation/` — Domain Validation

### Example

`AttendanceValidator.java`

**Responsibilities**

* Custom validations not covered by annotations

Examples:

* Semester validity
* Course ownership checks

---

## 4️⃣ DOMAIN-SPECIFIC DESIGN

Below are **explicit expectations per domain**.

---

# 🔐 `auth/` DOMAIN

### Purpose

Authentication & token lifecycle

### Files

```
auth/
├── controller/AuthController.java
├── service/AuthService.java
├── dto/LoginRequest.java
├── dto/LoginResponse.java
├── entity/RefreshToken.java
└── repository/RefreshTokenRepository.java
```

### Rules

* JWT is stateless
* Refresh tokens stored server-side
* Password hashing via BCrypt

---

# 👤 `users/` DOMAIN

### Purpose

User management

### Entities

* User
* Role (enum)

### APIs

* Create user
* Fetch profile
* Update profile
* Admin user listing

---

# 📚 `attendance/` DOMAIN

### Purpose

Attendance tracking

### Key APIs

```
GET  /students/{id}/attendance-summary
POST /faculty/attendance/submit
GET  /faculty/{id}/attendance
```

### Backend computes:

* Attendance percentage
* Eligibility status

Frontend **must not calculate this**.

---

# 📘 `courses/` DOMAIN

### Purpose

Academic structure

### Entities

* Course
* Department
* Semester

### Rules

* Course ownership enforced at service layer
* No free-form IDs

---

# 📝 `grades/` DOMAIN

### Purpose

Grade management

### APIs

* Upload grades (bulk)
* Fetch student grades
* Faculty grade submission

### Backend computes:

* GPA
* Grade points

---

# 💰 `fees/` DOMAIN

### Purpose

Fee tracking (no payment gateway initially)

### APIs

* Fee status
* Due dates
* Receipt generation

---

# 📢 `notices/` DOMAIN

### Purpose

Announcements

### Rules

* Target roles
* Expiry support

---

## 5️⃣ `common/` — SHARED INFRASTRUCTURE

---

## `common/config/`

### Files

```
SwaggerConfig.java
CorsConfig.java
JacksonConfig.java
```

**Purpose**

* Cross-cutting config

---

## `common/security/`

### Files

```
JwtFilter.java
SecurityConfig.java
UserDetailsServiceImpl.java
```

**Rules**

* Authorization enforced here
* Roles checked via annotations

---

## `common/exception/`

### Files

```
GlobalExceptionHandler.java
DomainException.java
```

**Purpose**

* Centralized error handling

---

## `common/response/`

### Standard Response Wrapper

```java
ApiResponse<T> {
  boolean success;
  T data;
  ApiError error;
}
```

**Rule**

> All controllers return this wrapper.

---

## `common/utils/`

### Examples

* Date utilities
* Token helpers

---

## 6️⃣ ERROR HANDLING CONTRACT

### Backend Error Format

```json
{
  "code": "ATTENDANCE_ALREADY_SUBMITTED",
  "message": "Attendance already submitted for this date",
  "details": {}
}
```

### Rules

* Error codes are stable
* Messages are user-safe
* Stack traces never exposed

---

## 7️⃣ SECURITY & ROLES

### Roles

```
STUDENT
FACULTY
ADMIN
```

### Enforcement

* Method-level (`@PreAuthorize`)
* Service-level checks

Frontend **assumes backend is authoritative**.

---

## 8️⃣ DATABASE & MIGRATIONS

### Tool

* Flyway

### Rule

* One migration per change
* No schema auto-update

---

## 9️⃣ API VERSIONING

```
/api/v1/...
```

Breaking change?
→ `/api/v2/...`

---

## 🔟 CODING AGENT EXECUTION INSTRUCTIONS

When generating code:

1. **Create domain folder**
2. **Generate entities**
3. **Generate DTOs**
4. **Generate repositories**
5. **Generate services**
6. **Generate controllers**
7. **Wire security**
8. **Add Swagger docs**
9. **Add validations**
10. **Write zero frontend logic**

---

## 🧠 FINAL MENTAL MODEL FOR BACKEND AGENT

```
HTTP → Controller
Rules → Service
DB → Repository
API Shape → DTO
Conversion → Mapper
Security → common/security
```

---