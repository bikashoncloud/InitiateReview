# Initiate Dating App - Technical Project Manager Guide
## Architecture, Management & Delivery Guide for Java Architect

**Prepared For:** Bikash Kumar (Java Developer/Architect → Project Manager)
**Date:** January 17, 2026
**Purpose:** Complete technical architecture and management guide
**Your Background:** Java developer with design/architecture experience
**Your Role:** Managing Flutter (Mobile) + ASP.NET (Backend) developers

---

## TABLE OF CONTENTS

1. [Technology Stack Translation (Java → Current Stack)](#tech-stack-translation)
2. [Complete System Architecture](#system-architecture)
3. [Sequence Diagrams for Key Flows](#sequence-diagrams)
4. [Component Architecture](#component-architecture)
5. [Database Architecture](#database-architecture)
6. [API Design & Contracts](#api-design)
7. [Developer Management Strategy](#developer-management)
8. [Week-by-Week Technical Deliverables](#weekly-deliverables)
9. [Quality Gates & Review Checkpoints](#quality-gates)
10. [Risk Management & Technical Debt](#risk-management)
11. [Admin Website Integration](#admin-website)
12. [Production Deployment Architecture](#deployment-architecture)

---

## TECH STACK TRANSLATION

### For Your Java Background

Since you're familiar with Java ecosystem, here's the translation:

| Java/Spring Boot | This Project | Similarity |
|------------------|--------------|------------|
| **Spring Boot** | ASP.NET 4.7.2 Web API | Web framework |
| **Spring MVC** | ASP.NET MVC Controllers | Request handling |
| **Spring Data JPA** | MySQL Stored Procedures | Data access (different approach!) |
| **Hibernate** | ~~Entity Framework~~ (Not used) | ORM (they chose SPs instead) |
| **@Service** | `initiate.BAL` interfaces | Business layer |
| **@Repository** | `initiate.DAL` services | Data access layer |
| **Maven/Gradle** | NuGet | Dependency management |
| **JUnit** | xUnit/NUnit | Testing framework |
| **Mockito** | Moq | Mocking framework |
| **Spring DI** | Unity Container | Dependency injection |
| **ModelMapper** | AutoMapper | Object mapping |
| **Logback/SLF4J** | Serilog (needs to be added) | Logging |
| **Redis** | StackExchange.Redis (needs to be added) | Caching |
| **WebSocket** | SignalR (planned) | Real-time communication |
| **Android (Java/Kotlin)** | Flutter (Dart) | Mobile development |

---

## SYSTEM ARCHITECTURE

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT TIER                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐           ┌─────────────────┐            │
│  │  Flutter Mobile │           │   Admin Web     │            │
│  │      App        │           │   Portal        │            │
│  │   (Android/iOS) │           │  (ASP.NET MVC)  │            │
│  └────────┬────────┘           └────────┬────────┘            │
│           │                              │                      │
└───────────┼──────────────────────────────┼──────────────────────┘
            │                              │
            │  REST API (JSON)             │  Web Interface
            │  + SignalR (WebSocket)       │
            │                              │
┌───────────┼──────────────────────────────┼──────────────────────┐
│           ▼                              ▼                      │
│  ┌──────────────────────────────────────────────────┐          │
│  │         APPLICATION TIER (Web Server)            │          │
│  ├──────────────────────────────────────────────────┤          │
│  │                                                   │          │
│  │  ┌───────────────────────────────────────────┐  │          │
│  │  │    initiate.API (Presentation Layer)      │  │          │
│  │  │  ┌─────────────────────────────────────┐ │  │          │
│  │  │  │  Controllers (API Endpoints)        │ │  │          │
│  │  │  │  - AuthController                   │ │  │          │
│  │  │  │  - ProfileController                │ │  │          │
│  │  │  │  - DiscoveryController (TODO)       │ │  │          │
│  │  │  │  - MatchController (TODO)           │ │  │          │
│  │  │  │  - MessageController (TODO)         │ │  │          │
│  │  │  └─────────────────────────────────────┘ │  │          │
│  │  │                     │                     │  │          │
│  │  │  ┌─────────────────▼───────────────────┐ │  │          │
│  │  │  │  Middleware Pipeline                │ │  │          │
│  │  │  │  - Authentication (OAuth)           │ │  │          │
│  │  │  │  - Authorization                    │ │  │          │
│  │  │  │  - Exception Handling (TODO)        │ │  │          │
│  │  │  │  - Rate Limiting (TODO)             │ │  │          │
│  │  │  │  - Logging (TODO)                   │ │  │          │
│  │  │  └─────────────────────────────────────┘ │  │          │
│  │  └───────────────────────────────────────────┘  │          │
│  │                     │                            │          │
│  │  ┌──────────────────▼──────────────────────┐   │          │
│  │  │  initiate.BAL (Business Layer)          │   │          │
│  │  │  ┌────────────────────────────────────┐ │   │          │
│  │  │  │  Repository Interfaces             │ │   │          │
│  │  │  │  - IAuthRepo                       │ │   │          │
│  │  │  │  - IProfileRepo                    │ │   │          │
│  │  │  │  - IMatchRepo (TODO)               │ │   │          │
│  │  │  │  - IMessageRepo (TODO)             │ │   │          │
│  │  │  └────────────────────────────────────┘ │   │          │
│  │  │  ┌────────────────────────────────────┐ │   │          │
│  │  │  │  DTOs (Data Transfer Objects)      │ │   │          │
│  │  │  │  - Request DTOs                    │ │   │          │
│  │  │  │  - Response DTOs                   │ │   │          │
│  │  │  └────────────────────────────────────┘ │   │          │
│  │  │  ┌────────────────────────────────────┐ │   │          │
│  │  │  │  Domain Models                     │ │   │          │
│  │  │  │  - tbl_userLogin                   │ │   │          │
│  │  │  │  - User (TODO)                     │ │   │          │
│  │  │  └────────────────────────────────────┘ │   │          │
│  │  └─────────────────────────────────────────┘   │          │
│  │                     │                           │          │
│  │  ┌──────────────────▼──────────────────────┐   │          │
│  │  │  initiate.DAL (Data Access Layer)       │   │          │
│  │  │  ┌────────────────────────────────────┐ │   │          │
│  │  │  │  Service Implementations           │ │   │          │
│  │  │  │  - AuthService (IAuthRepo)         │ │   │          │
│  │  │  │  - ProfileService (IProfileRepo)   │ │   │          │
│  │  │  └────────────────────────────────────┘ │   │          │
│  │  │  ┌────────────────────────────────────┐ │   │          │
│  │  │  │  MySqlUtilityDb                    │ │   │          │
│  │  │  │  - Stored Procedure Executor       │ │   │          │
│  │  │  │  - DataReader/DataSet methods      │ │   │          │
│  │  │  │  - Thread-safe operations          │ │   │          │
│  │  │  └────────────────────────────────────┘ │   │          │
│  │  └─────────────────────────────────────────┘   │          │
│  │                     │                           │          │
│  └─────────────────────┼───────────────────────────┘          │
│                        │                                       │
└────────────────────────┼───────────────────────────────────────┘
                         │
                         │  TCP/IP (MySQL Protocol)
                         │
┌────────────────────────▼───────────────────────────────────────┐
│                     DATA TIER                                  │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │          MySQL Database (u438054979_initiate)           │  │
│  │  ┌──────────────────────────────────────────────────┐   │  │
│  │  │  Tables                                          │   │  │
│  │  │  - SexualInterestMaster (implemented)           │   │  │
│  │  │  - Users (TODO)                                 │   │  │
│  │  │  - Profiles (TODO)                              │   │  │
│  │  │  - Photos (TODO)                                │   │  │
│  │  │  - Matches (TODO)                               │   │  │
│  │  │  - Likes (TODO)                                 │   │  │
│  │  │  - Messages (TODO)                              │   │  │
│  │  │  - ~10 more tables                              │   │  │
│  │  └──────────────────────────────────────────────────┘   │  │
│  │  ┌──────────────────────────────────────────────────┐   │  │
│  │  │  Stored Procedures                               │   │  │
│  │  │  - API_V1_spSexualInterest (implemented)        │   │  │
│  │  │  - API_V1_spSendOtp (TODO)                      │   │  │
│  │  │  - API_V1_spVerifyOtp (TODO)                    │   │  │
│  │  │  - API_V1_spGetUserProfile (TODO)               │   │  │
│  │  │  - API_V1_spUpdateUserProfile (TODO)            │   │  │
│  │  │  - ~50 more procedures                           │   │  │
│  │  └──────────────────────────────────────────────────┘   │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │          Redis Cache (TODO - Add Later)                 │  │
│  │  - Session management                                   │  │
│  │  - Daily like counters                                  │  │
│  │  - Discovery feed cache                                 │  │
│  │  - Lookup data cache                                    │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │          AWS S3 / File Storage (TODO)                   │  │
│  │  - Profile photos                                       │  │
│  │  - Story media                                          │  │
│  │  - Verification selfies                                 │  │
│  └─────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

### Java Comparison

**Think of it like this (Spring Boot equivalent):**

```java
// JAVA SPRING BOOT (What you know)
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    @Autowired
    private AuthService authService;  // Spring DI

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody LoginRequest request) {
        return authService.login(request);
    }
}

@Service
public class AuthService {
    @Autowired
    private UserRepository userRepository;  // Spring Data JPA

    public AuthResponse login(LoginRequest request) {
        User user = userRepository.findByEmail(request.getEmail());
        // Business logic
    }
}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    User findByEmail(String email);  // JPA auto-generates SQL
}
```

**ASP.NET Equivalent (Current project):**

```csharp
// ASP.NET WEB API (Current project)
[RoutePrefix("api/auth")]
public class AuthController : ApiController {
    private readonly IAuthRepo _authService;  // Unity DI

    [HttpPost]
    [Route("login")]
    public IHttpActionResult Login(LoginRequest request) {
        return Ok(_authService.Login(request));
    }
}

// Business layer interface
public interface IAuthRepo {
    AuthResponse Login(LoginRequest request);
}

// Data layer implementation
public class AuthService : IAuthRepo {
    private readonly IMySqlUtilityDb _db;  // Manual DB utility (not JPA)

    public AuthResponse Login(LoginRequest request) {
        // Call stored procedure (no ORM!)
        var reader = _db.fn_DataReader("API_V1_spLogin", parameters);
        // Manual mapping (no Hibernate auto-mapping)
        while (reader.Read()) {
            // Map columns to object manually
        }
    }
}
```

**Key Difference:** No JPA/Hibernate equivalent - using stored procedures directly!

---

## COMPONENT ARCHITECTURE

### 3-Layer Architecture Breakdown

```
┌───────────────────────────────────────────────────────────────┐
│                     PRESENTATION LAYER                        │
│                    (initiate.API project)                     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│  Controllers/                                                 │
│  ├── AuthController.cs                                        │
│  │   ├── POST /api/auth/sendotp                             │
│  │   ├── POST /api/auth/verifyotp                           │
│  │   └── POST /api/auth/logout                              │
│  │                                                            │
│  ├── ProfileController.cs                                     │
│  │   ├── GET /api/profile                                    │
│  │   ├── POST /api/profile/update                           │
│  │   ├── GET /api/profile/interests                         │
│  │   └── POST /api/profile/photo/upload (TODO)              │
│  │                                                            │
│  ├── DiscoveryController.cs (TODO - Week 2)                  │
│  │   ├── GET /api/discovery/nearby                          │
│  │   └── POST /api/discovery/swipe                          │
│  │                                                            │
│  ├── MatchController.cs (TODO - Week 2)                      │
│  │   ├── GET /api/match/list                                │
│  │   └── GET /api/match/who-liked-me                        │
│  │                                                            │
│  └── MessageController.cs (TODO - Week 3)                    │
│      ├── GET /api/message/conversations                      │
│      ├── GET /api/message/chat/{matchId}                     │
│      └── POST /api/message/send                              │
│                                                               │
│  App_Start/                                                   │
│  ├── UnityConfig.cs        ← DI container setup              │
│  ├── WebApiConfig.cs       ← Routing configuration          │
│  └── Startup.cs            ← OAuth/OWIN setup                │
│                                                               │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            │ Interface boundary
                            │
┌───────────────────────────▼───────────────────────────────────┐
│                    BUSINESS LAYER (BAL)                       │
│                    (initiate.BAL project)                     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│  Repository/ (Interfaces - like Java @Service layer)         │
│  ├── IAuthRepo.cs                                            │
│  │   ├── vm_userLogin sendOtp(string MobileNo)              │
│  │   ├── vm_userLogin verifyOtp(string MobileNo, OTP)       │
│  │   └── vm_AuthComponent LoginWithOTP(...)                 │
│  │                                                            │
│  ├── IProfileRepo.cs                                         │
│  │   ├── List<SexualInterestResDTO> GetSexualInterestList() │
│  │   ├── List<userProfileResDTO> GetUserProfileByID(id)     │
│  │   └── vmResponseMessage UpdateUserProfileByID(...)       │
│  │                                                            │
│  └── IMySqlUtilityDb.cs (Database utility interface)        │
│                                                               │
│  DTO/ (Data Transfer Objects - like Java DTOs)               │
│  ├── Request DTOs                                            │
│  │   ├── vm_updateProfileReqDTO                             │
│  │   └── userProfileReqDTOs                                 │
│  │                                                            │
│  └── Response DTOs                                           │
│      ├── SexualInterestResDTO                               │
│      ├── userProfileResDTO                                   │
│      ├── vm_userLogin                                        │
│      └── vmResponseMessage                                   │
│                                                               │
│  Domain/ (Domain entities - simple POJOs)                    │
│  └── tbl_userLogin.cs                                        │
│                                                               │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            │ Implementation boundary
                            │
┌───────────────────────────▼───────────────────────────────────┐
│                   DATA ACCESS LAYER (DAL)                     │
│                    (initiate.DAL project)                     │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│  Services/ (Repository implementations - like Java @Repository)│
│  ├── AuthService.cs (implements IAuthRepo)                   │
│  │   └── Calls stored procedures via MySqlUtilityDb         │
│  │                                                            │
│  └── ProfileService.cs (implements IProfileRepo)             │
│      └── Calls stored procedures via MySqlUtilityDb          │
│                                                               │
│  Setting/                                                     │
│  └── MySqlUtilityDb.cs (implements IMySqlUtilityDb)         │
│      ├── DataSet Dp_DataSet(procedure, params)              │
│      ├── IDataReader fn_DataReader(procedure, params)       │
│      ├── DataTable fn_DataTable(procedure, params)          │
│      ├── int fn_ExecuteNonQuery(procedure, params)          │
│      └── object fn_ExecuteScalar(procedure, params)         │
│                                                               │
│      ↓ Uses connection pooling & thread-safe operations     │
│                                                               │
└───────────────────────────┬───────────────────────────────────┘
                            │
                            │ JDBC-like connection
                            │
┌───────────────────────────▼───────────────────────────────────┐
│                    MySQL DATABASE                             │
│                                                               │
│  Schema: u438054979_initiate                                 │
│  Stored Procedures (like Oracle PL/SQL procedures)           │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

### Comparison to Java Architecture You Know

| Layer | Java (Spring Boot) | This Project | Notes |
|-------|-------------------|--------------|-------|
| **Controller** | `@RestController` | `ApiController` | Same role |
| **Service** | `@Service` | Interface in BAL | Contract definition |
| **Service Impl** | `@Service` class | Service in DAL | Implementation |
| **Repository** | `JpaRepository<T>` | Methods in DAL Service | Different! |
| **Entity** | `@Entity` classes | ~~Not used~~ | Using SPs instead |
| **DTO** | Plain classes | Plain classes | Same |
| **DI** | `@Autowired` | Unity container | Same concept |

---

## SEQUENCE DIAGRAMS

### 1. User Registration & OTP Flow

```
Java analogy: Like Spring Security with custom OTP authentication

User          Flutter App       API Controller    AuthService       MySQL DB        SMS Service
 │                │                   │                │                │                │
 │  Enter phone   │                   │                │                │                │
 │ ──────────────>│                   │                │                │                │
 │                │                   │                │                │                │
 │                │  POST /api/auth/sendotp          │                │                │
 │                │  { mobileNo }     │                │                │                │
 │                │──────────────────>│                │                │                │
 │                │                   │                │                │                │
 │                │                   │  sendOtp()     │                │                │
 │                │                   │───────────────>│                │                │
 │                │                   │                │                │                │
 │                │                   │                │  CALL API_V1_spSendOtp(@MobileNo)│
 │                │                   │                │───────────────>│                │
 │                │                   │                │                │                │
 │                │                   │                │  1. Generate OTP (6 digits)    │
 │                │                   │                │  2. Store in DB with expiry    │
 │                │                   │                │  3. Return OTP + UserId        │
 │                │                   │                │<───────────────│                │
 │                │                   │                │                │                │
 │                │                   │                │  Send SMS      │                │
 │                │                   │                │─────────────────────────────────>│
 │                │                   │                │                │                │
 │                │                   │  Response      │                │  SMS sent      │
 │                │                   │  { success }   │                │                │
 │                │                   │<───────────────│                │<───────────────│
 │                │                   │                │                │                │
 │                │  200 OK           │                │                │                │
 │                │  { success, msg } │                │                │                │
 │                │<──────────────────│                │                │                │
 │                │                   │                │                │                │
 │  Show OTP      │                   │                │                │                │
 │  input screen  │                   │                │                │                │
 │<───────────────│                   │                │                │                │
 │                │                   │                │                │                │
 │  Enter OTP     │                   │                │                │                │
 │ ──────────────>│                   │                │                │                │
 │                │                   │                │                │                │
 │                │  POST /api/auth/verifyotp        │                │                │
 │                │  { mobileNo, otp }│                │                │                │
 │                │──────────────────>│                │                │                │
 │                │                   │                │                │                │
 │                │                   │  verifyOtp()   │                │                │
 │                │                   │───────────────>│                │                │
 │                │                   │                │                │                │
 │                │                   │                │  CALL API_V1_spVerifyOtp(@MobileNo, @OTP)│
 │                │                   │                │───────────────>│                │
 │                │                   │                │                │                │
 │                │                   │                │  1. Validate OTP matches      │
 │                │                   │                │  2. Check expiry (5 min)      │
 │                │                   │                │  3. Return User + ProfileStatus│
 │                │                   │                │<───────────────│                │
 │                │                   │                │                │                │
 │                │                   │  Generate JWT Token (180 days) │                │
 │                │                   │  Claims: UserId, MobileNo     │                │
 │                │                   │<───────────────│                │                │
 │                │                   │                │                │                │
 │                │  200 OK           │                │                │                │
 │                │  { token, userId, isProfileCompleted }    │                │
 │                │<──────────────────│                │                │                │
 │                │                   │                │                │                │
 │  Store token   │                   │                │                │                │
 │  in storage    │                   │                │                │                │
 │<───────────────│                   │                │                │                │
 │                │                   │                │                │                │
 │  Navigate to   │                   │                │                │                │
 │  Profile Setup │                   │                │                │                │
 │<───────────────│                   │                │                │                │
```

**Java Equivalent:**
- Like Spring Security OAuth2 with custom authentication provider
- Token stored in JWT (like Spring Security JWT)
- Claims like Spring Security UserDetails

---

### 2. Profile Creation Flow (Week 1-2 Priority)

```
User      Flutter App    API Controller   ProfileService   MySQL DB    S3 Storage
 │            │                │                │              │            │
 │  Fill      │                │                │              │            │
 │  profile   │                │                │              │            │
 │  form      │                │                │              │            │
 │───────────>│                │                │              │            │
 │            │                │                │              │            │
 │  Select    │                │                │              │            │
 │  photos    │                │                │              │            │
 │  (1-6)     │                │                │              │            │
 │───────────>│                │                │              │            │
 │            │                │                │              │            │
 │            │  Compress photos (client-side)  │              │            │
 │            │  <500KB each   │                │              │            │
 │            │                │                │              │            │
 │            │  Upload photos (one by one or batch)           │            │
 │            │─────────────────────────────────────────────────────────────>│
 │            │                │                │              │            │
 │            │  S3 URLs       │                │              │            │
 │            │<────────────────────────────────────────────────────────────│
 │            │  [url1, url2..]│                │              │            │
 │            │                │                │              │            │
 │            │  POST /api/profile/update                      │            │
 │            │  Authorization: Bearer {token}                 │            │
 │            │  {                │                │              │            │
 │            │    userId: 123,   │                │              │            │
 │            │    fullName: "John",              │              │            │
 │            │    gender: "M",   │                │              │            │
 │            │    dob: "1995-01-01",             │              │            │
 │            │    bio: "...",    │                │              │            │
 │            │    photos: [url1, url2],          │              │            │
 │            │    interests: [1,2,3],            │              │            │
 │            │    latitude: 19.076,              │              │            │
 │            │    longitude: 72.877              │              │            │
 │            │  }                │                │              │            │
 │            │──────────────────>│                │              │            │
 │            │                   │                │              │            │
 │            │                   │  Extract UserId from JWT token │           │
 │            │                   │  (Claims.NameIdentifier)      │           │
 │            │                   │                │              │            │
 │            │                   │  UpdateProfile()             │            │
 │            │                   │───────────────>│              │            │
 │            │                   │                │              │            │
 │            │                   │                │  CALL API_V1_spUpdateUserProfile│
 │            │                   │                │  (                      │
 │            │                   │                │    @UserId,             │
 │            │                   │                │    @FullName,           │
 │            │                   │                │    @Gender,             │
 │            │                   │                │    @DOB,                │
 │            │                   │                │    @Bio,                │
 │            │                   │                │    @HeightCm,           │
 │            │                   │                │    @City,               │
 │            │                   │                │    @LookingFor,         │
 │            │                   │                │    @Latitude,           │
 │            │                   │                │    @Longitude,          │
 │            │                   │                │    @Photos JSON array   │
 │            │                   │                │  )                      │
 │            │                   │                │─────────────>           │
 │            │                   │                │              │            │
 │            │                   │                │  1. INSERT/UPDATE users │
 │            │                   │                │  2. Parse photos JSON   │
 │            │                   │                │  3. INSERT photos       │
 │            │                   │                │  4. Return success      │
 │            │                   │                │<─────────────│           │
 │            │                   │                │              │            │
 │            │                   │  Success response            │            │
 │            │                   │<───────────────│              │            │
 │            │                   │                │              │            │
 │            │  200 OK           │                │              │            │
 │            │  { success: true }│                │              │            │
 │            │<──────────────────│                │              │            │
 │            │                   │                │              │            │
 │  Navigate  │                   │                │              │            │
 │  to Home   │                   │                │              │            │
 │<───────────│                   │                │              │            │
```

**Java Equivalent:**
```java
// Like Spring REST + JPA transaction
@PostMapping("/profile/update")
@Transactional  // In ASP.NET: Done in stored procedure
public ResponseEntity<ProfileResponse> updateProfile(
    @RequestBody UpdateProfileRequest request,
    @AuthenticationPrincipal UserDetails userDetails) {

    // In your project, this is all in stored procedure!
    userRepository.save(user);
    photoRepository.saveAll(photos);

    return ResponseEntity.ok(response);
}
```

---

### 3. Swipe & Match Detection Flow (Week 2 Priority)

```
User A      Flutter App    DiscoveryController   MatchingService    MySQL DB      User B App
 │              │                  │                    │               │               │
 │  Swipe right │                  │                    │               │               │
 │  on User B   │                  │                    │               │               │
 │─────────────>│                  │                    │               │               │
 │              │                  │                    │               │               │
 │              │  POST /api/discovery/swipe           │               │               │
 │              │  Authorization: Bearer {token}       │               │               │
 │              │  {                │                    │               │               │
 │              │    targetUserId: B,                   │               │               │
 │              │    action: "like" │                    │               │               │
 │              │  }                │                    │               │               │
 │              │──────────────────>│                    │               │               │
 │              │                   │                    │               │               │
 │              │                   │  Extract UserA from JWT            │               │
 │              │                   │                    │               │               │
 │              │                   │  ProcessSwipe(A, B, "like")       │               │
 │              │                   │───────────────────>│               │               │
 │              │                   │                    │               │               │
 │              │                   │                    │  BEGIN TRANSACTION           │
 │              │                   │                    │──────────────>│               │
 │              │                   │                    │               │               │
 │              │                   │                    │  1. Check daily limit (free users)│
 │              │                   │                    │  2. INSERT into likes (A→B) │
 │              │                   │                    │  3. SELECT reciprocal like (B→A)│
 │              │                   │                    │               │               │
 │              │                   │                    │  IF reciprocal EXISTS:      │
 │              │                   │                    │  4. INSERT into matches (A,B)│
 │              │                   │                    │  5. INSERT notifications x2 │
 │              │                   │                    │               │               │
 │              │                   │                    │  COMMIT       │               │
 │              │                   │                    │<──────────────│               │
 │              │                   │                    │               │               │
 │              │                   │  Response:         │               │               │
 │              │                   │  { isMatch: true } │               │               │
 │              │                   │<───────────────────│               │               │
 │              │                   │                    │               │               │
 │              │                   │  IF isMatch:       │               │               │
 │              │                   │  Notify User B via SignalR/Push   │               │
 │              │                   │──────────────────────────────────────────────────>│
 │              │                   │                    │               │               │
 │              │  200 OK           │                    │               │  Push notification│
 │              │  {                │                    │               │  "It's a Match!"│
 │              │    success: true, │                    │               │<──────────────│
 │              │    isMatch: true, │                    │               │               │
 │              │    matchId: 456   │                    │               │               │
 │              │  }                │                    │               │               │
 │              │<──────────────────│                    │               │               │
 │              │                   │                    │               │               │
 │  Show "Match"│                   │                    │               │               │
 │  celebration │                   │                    │               │               │
 │  dialog      │                   │                    │               │               │
 │<─────────────│                   │                    │               │               │
```

**Java Equivalent:**
```java
// Like implementing Tinder-style matching in Spring Boot
@Service
@Transactional
public class MatchingService {

    @Autowired
    private LikeRepository likeRepository;

    @Autowired
    private MatchRepository matchRepository;

    public SwipeResult processSwipe(Long userA, Long userB, boolean isLike) {
        // Check daily limit
        // Save like
        // Check for reciprocal like
        // If mutual: create match + send notifications
    }
}
```

**In your project:** All this logic is in a stored procedure!

---

### 4. Real-Time Chat Flow with SignalR (Week 3)

```
User A      Flutter App    MessageController   MessageService   ChatHub (SignalR)   User B App
 │              │                  │                  │                  │                │
 │              │  (On app start) Connect to SignalR Hub                │                │
 │              │─────────────────────────────────────────────────────────>│                │
 │              │                  │                  │                  │                │
 │              │                  │                  │  AddToGroup("userA")            │
 │              │<─────────────────────────────────────────────────────────│                │
 │              │  Connected       │                  │                  │                │
 │              │                  │                  │                  │  (User B also connected)│
 │              │                  │                  │                  │<───────────────│
 │              │                  │                  │                  │                │
 │  Type msg    │                  │                  │                  │                │
 │  "Hello"     │                  │                  │                  │                │
 │─────────────>│                  │                  │                  │                │
 │              │                  │                  │                  │                │
 │              │  Hub.SendMessage(userB, "Hello")   │                  │                │
 │              │────────────────────────────────────────────────────────>│                │
 │              │  (SignalR call)  │                  │                  │                │
 │              │                  │                  │                  │                │
 │              │                  │                  │  1. Validate match exists       │
 │              │                  │                  │  2. Check female-first rule     │
 │              │                  │                  │  3. Save to DB                  │
 │              │                  │                  │  4. Get msgId                   │
 │              │                  │                  │                  │                │
 │              │                  │                  │  Push to User B's group         │
 │              │                  │                  │  ReceiveMessage(sender:A, msg)  │
 │              │                  │                  │─────────────────────────────────>│
 │              │                  │                  │                  │                │
 │              │  Message         │                  │                  │  New message  │
 │              │  confirmed       │                  │                  │  appears      │
 │              │<────────────────────────────────────────────────────────│  instantly    │
 │              │  { msgId, status }                  │                  │<──────────────│
 │              │                  │                  │                  │                │
 │  Show msg    │                  │                  │                  │  Show msg     │
 │  as "sent"   │                  │                  │                  │  as "new"     │
 │<─────────────│                  │                  │                  │──────────────>│
 │              │                  │                  │                  │                │
 │              │                  │                  │  (User B reads message)         │
 │              │                  │                  │  Hub.MarkAsRead(msgId)          │
 │              │                  │                  │<─────────────────────────────────│
 │              │                  │                  │                  │                │
 │              │                  │                  │  Update DB: is_read = true     │
 │              │                  │                  │  Send read receipt to User A   │
 │              │                  │                  │─────────────────>│                │
 │              │                  │                  │                  │                │
 │  Show "Read" │                  │                  │  MessageRead    │                │
 │  indicator   │                  │                  │  { msgId }       │                │
 │<──────────────────────────────────────────────────────────────│                │
```

**Java Equivalent:**
- Like Spring WebSocket + STOMP
- Similar to WhatsApp backend message flow
- SignalR = Java WebSocket

---

### 5. Discovery Feed with Proximity Search (Week 2)

```
User        Flutter App    DiscoveryController   MatchingService   MySQL DB (Spatial)
 │              │                  │                    │                │
 │  Open        │                  │                    │                │
 │  Discovery   │                  │                    │                │
 │  screen      │                  │                    │                │
 │─────────────>│                  │                    │                │
 │              │                  │                    │                │
 │              │  Request location permission          │                │
 │              │  (if not granted)│                    │                │
 │              │                  │                    │                │
 │  Grant       │                  │                    │                │
 │  permission  │                  │                    │                │
 │<─────────────│                  │                    │                │
 │              │                  │                    │                │
 │              │  Get current location (GPS)           │                │
 │              │  { lat: 19.076, lon: 72.877 }         │                │
 │              │                  │                    │                │
 │              │  GET /api/discovery/nearby?radiusKm=50│                │
 │              │  Authorization: Bearer {token}        │                │
 │              │──────────────────>│                    │                │
 │              │                   │                    │                │
 │              │                   │  Extract UserId from JWT           │
 │              │                   │  Get user's location from DB or request│
 │              │                   │                    │                │
 │              │                   │  GetNearbyProfiles(userId, lat, lon, radius)│
 │              │                   │───────────────────>│                │
 │              │                   │                    │                │
 │              │                   │                    │  CALL API_V1_spGetNearbyUsers│
 │              │                   │                    │  WITH SPATIAL QUERY:         │
 │              │                   │                    │                │
 │              │                   │                    │  SELECT u.*,   │
 │              │                   │                    │  ST_Distance_Sphere(        │
 │              │                   │                    │    POINT(u.lon, u.lat),     │
 │              │                   │                    │    POINT(@lon, @lat)        │
 │              │                   │                    │  ) / 1000 AS distance_km    │
 │              │                   │                    │  FROM users u               │
 │              │                   │                    │  LEFT JOIN likes l          │
 │              │                   │                    │    ON l.from_user = @userId │
 │              │                   │                    │    AND l.to_user = u.id     │
 │              │                   │                    │  WHERE l.id IS NULL         │
 │              │                   │                    │    AND u.id != @userId      │
 │              │                   │                    │    AND u.is_verified = 1    │
 │              │                   │                    │  HAVING distance_km <= @radius│
 │              │                   │                    │  ORDER BY                   │
 │              │                   │                    │    u.is_verified DESC,      │
 │              │                   │                    │    distance_km ASC          │
 │              │                   │                    │  LIMIT 50                   │
 │              │                   │                    │──────────────>│
 │              │                   │                    │                │
 │              │                   │                    │  Result: 50 profiles       │
 │              │                   │                    │<──────────────│
 │              │                   │                    │                │
 │              │                   │  [                 │                │
 │              │                   │    { id, name, age, photos[], distance },│
 │              │                   │    { id, name, age, photos[], distance },│
 │              │                   │    ...             │                │
 │              │                   │  ]                 │                │
 │              │                   │<───────────────────│                │
 │              │                   │                    │                │
 │              │  200 OK           │                    │                │
 │              │  [ profiles ]     │                    │                │
 │              │<──────────────────│                    │                │
 │              │                   │                    │                │
 │  Display     │                   │                    │                │
 │  swipeable   │                   │                    │                │
 │  cards       │                   │                    │                │
 │<─────────────│                   │                    │                │
```

**Java Equivalent:**
```java
// Like implementing geospatial search in Spring Boot + PostGIS
@Query(value = "SELECT u FROM User u WHERE " +
    "ST_Distance_Sphere(u.location, :userLocation) <= :radiusMeters")
List<User> findNearbyUsers(
    @Param("userLocation") Point userLocation,
    @Param("radiusMeters") double radiusMeters
);
```

---

## DATABASE ARCHITECTURE

### MySQL Schema Design (Complete - What Needs to Be Built)

```sql
-- Think of this like your Java JPA entity relationships

-- ================================================================
-- CORE TABLES (Week 1-2 Priority)
-- ================================================================

-- Users table (like @Entity User in Java)
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    mobile_no VARCHAR(15) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE,
    password_hash VARCHAR(255),           -- For email login (future)
    full_name VARCHAR(100) NOT NULL,
    gender ENUM('male', 'female', 'other') NOT NULL,
    dob DATE NOT NULL,
    age INT GENERATED ALWAYS AS (YEAR(CURDATE()) - YEAR(dob)) STORED,
    bio TEXT,
    profession VARCHAR(100),
    height_cm INT,
    city VARCHAR(100),
    latitude DECIMAL(10, 7) NOT NULL,     -- For proximity search
    longitude DECIMAL(10, 7) NOT NULL,
    looking_for VARCHAR(50),               -- 'relationship', 'casual', 'friends'
    languages VARCHAR(255),                -- Comma-separated
    interests VARCHAR(500),                -- Comma-separated interest IDs
    is_verified BOOLEAN DEFAULT FALSE,     -- Selfie verification status
    is_premium BOOLEAN DEFAULT FALSE,
    premium_expires_at TIMESTAMP NULL,
    last_active TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,

    -- Indexes (CRITICAL for performance!)
    INDEX idx_location (latitude, longitude),        -- For proximity search
    INDEX idx_verified_active (is_verified, last_active),  -- For discovery ordering
    INDEX idx_mobile (mobile_no),                    -- For login
    SPATIAL INDEX idx_spatial (latitude, longitude)  -- MySQL spatial index
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Profile photos (like @OneToMany in Java)
CREATE TABLE user_photos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    photo_url VARCHAR(500) NOT NULL,      -- S3 URL
    display_order INT DEFAULT 0,           -- 1st photo = profile pic
    is_verified BOOLEAN DEFAULT FALSE,     -- Passed content moderation
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user (user_id, display_order)
) ENGINE=InnoDB;

-- Likes (swipe actions) - like Junction table in Java
CREATE TABLE likes (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    from_user_id INT NOT NULL,
    to_user_id INT NOT NULL,
    liked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (from_user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (to_user_id) REFERENCES users(id) ON DELETE CASCADE,

    UNIQUE KEY unique_like (from_user_id, to_user_id),  -- Prevent duplicate likes
    INDEX idx_from_user_date (from_user_id, liked_at),  -- For daily limit check
    INDEX idx_to_user (to_user_id)                      -- For "who liked me"
) ENGINE=InnoDB;

-- Matches (mutual likes) - like @ManyToMany in Java
CREATE TABLE matches (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user1_id INT NOT NULL,
    user2_id INT NOT NULL,
    matched_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,        -- For unmatch feature

    FOREIGN KEY (user1_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (user2_id) REFERENCES users(id) ON DELETE CASCADE,

    UNIQUE KEY unique_match (user1_id, user2_id),
    INDEX idx_user1 (user1_id, matched_at),
    INDEX idx_user2 (user2_id, matched_at)
) ENGINE=InnoDB;

-- Messages (chat) - High volume table!
CREATE TABLE messages (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    match_id INT NOT NULL,
    sender_id INT NOT NULL,
    receiver_id INT NOT NULL,
    content TEXT NOT NULL,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP NULL,

    FOREIGN KEY (match_id) REFERENCES matches(id) ON DELETE CASCADE,
    FOREIGN KEY (sender_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (receiver_id) REFERENCES users(id) ON DELETE CASCADE,

    INDEX idx_match_date (match_id, sent_at),
    INDEX idx_receiver_unread (receiver_id, is_read, sent_at)
) ENGINE=InnoDB
PARTITION BY RANGE (YEAR(sent_at) * 100 + MONTH(sent_at)) (
    PARTITION p202601 VALUES LESS THAN (202602),
    PARTITION p202602 VALUES LESS THAN (202603),
    PARTITION p202603 VALUES LESS THAN (202604)
    -- Add monthly
);

-- ================================================================
-- LOOKUP TABLES (Week 1 - Static data)
-- ================================================================

-- Sexual interests (already implemented!)
CREATE TABLE SexualInterestMaster (
    ID INT PRIMARY KEY AUTO_INCREMENT,
    InterestName VARCHAR(100),
    InterestDesc VARCHAR(500),
    DO INT,                                -- Display Order
    Sts CHAR(1)                           -- Status: A=Active, I=Inactive
) ENGINE=InnoDB;

-- Looking for options
CREATE TABLE looking_interests (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50) NOT NULL,
    icon VARCHAR(50),
    display_order INT DEFAULT 0
) ENGINE=InnoDB;

-- ================================================================
-- PREMIUM & SUBSCRIPTION TABLES (Week 4)
-- ================================================================

CREATE TABLE subscriptions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    plan_type ENUM('basic_premium', 'gold', 'platinum') NOT NULL,
    start_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    end_date TIMESTAMP NOT NULL,
    payment_method VARCHAR(50),
    payment_id VARCHAR(255),
    amount DECIMAL(10, 2),
    currency VARCHAR(3) DEFAULT 'INR',

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_user_active (user_id, end_date)
) ENGINE=InnoDB;

-- ================================================================
-- VERIFICATION TABLES (Week 4)
-- ================================================================

CREATE TABLE user_verifications (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    selfie_url VARCHAR(500) NOT NULL,      -- Verification photo
    status ENUM('pending', 'approved', 'rejected') DEFAULT 'pending',
    submitted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    reviewed_at TIMESTAMP NULL,
    reviewer_id INT NULL,
    rejection_reason TEXT NULL,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_status (status, submitted_at)
) ENGINE=InnoDB;

-- ================================================================
-- MODERATION TABLES (Week 4)
-- ================================================================

CREATE TABLE user_reports (
    id INT PRIMARY KEY AUTO_INCREMENT,
    reporter_id INT NOT NULL,
    reported_user_id INT NOT NULL,
    reason ENUM('fake_profile', 'inappropriate_content', 'harassment', 'spam', 'other'),
    description TEXT,
    status ENUM('pending', 'reviewed', 'action_taken') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (reporter_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (reported_user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_status (status, created_at),
    INDEX idx_reported_user (reported_user_id)
) ENGINE=InnoDB;

CREATE TABLE user_blocks (
    id INT PRIMARY KEY AUTO_INCREMENT,
    blocker_id INT NOT NULL,
    blocked_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (blocker_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (blocked_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_block (blocker_id, blocked_id),
    INDEX idx_blocker (blocker_id),
    INDEX idx_blocked (blocked_id)
) ENGINE=InnoDB;

-- ================================================================
-- STORIES TABLES (Week 5 - Optional for MVP)
-- ================================================================

CREATE TABLE stories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    media_type ENUM('photo', 'video') NOT NULL,
    media_url VARCHAR(500) NOT NULL,
    thumbnail_url VARCHAR(500),
    posted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,         -- 24 hours from posted_at
    views_count INT DEFAULT 0,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_expires (expires_at),
    INDEX idx_user_active (user_id, posted_at)
) ENGINE=InnoDB;

CREATE TABLE story_views (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    story_id INT NOT NULL,
    viewer_id INT NOT NULL,
    viewed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (story_id) REFERENCES stories(id) ON DELETE CASCADE,
    FOREIGN KEY (viewer_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_view (story_id, viewer_id),
    INDEX idx_story (story_id)
) ENGINE=InnoDB;
```

### Entity Relationship Diagram (ERD)

```
┌──────────────┐
│    USERS     │
├──────────────┤
│ PK id        │
│    mobile_no │
│    email     │
│    full_name │
│    gender    │
│    dob       │
│    latitude  │◄────┐
│    longitude │     │
│    ...       │     │
└──────┬───────┘     │
       │             │
       │ 1:N         │ For proximity
       │             │ search query
┌──────▼───────┐     │
│ USER_PHOTOS  │     │
├──────────────┤     │
│ PK id        │     │
│ FK user_id   │     │
│    photo_url │     │
│    order     │     │
└──────────────┘     │
                     │
┌─────────────┐      │
│    LIKES    │      │
├─────────────┤      │
│ PK id       │      │
│ FK from_user│──────┘
│ FK to_user  │──────┐
│    liked_at │      │
└─────────────┘      │
                     │
       ┌─────────────┘
       │
┌──────▼──────┐
│   MATCHES   │
├─────────────┤
│ PK id       │
│ FK user1_id │──┐
│ FK user2_id │  │
│  matched_at │  │
└─────────────┘  │
       │         │
       │ 1:N     │
       │         │
┌──────▼─────────▼──┐
│     MESSAGES      │
├───────────────────┤
│ PK id             │
│ FK match_id       │
│ FK sender_id      │
│ FK receiver_id    │
│    content        │
│    sent_at        │
│    is_read        │
└───────────────────┘
```

**Java Comparison:**
```java
// In Java JPA, you'd have:
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<UserPhoto> photos;

    @OneToMany(mappedBy = "fromUser")
    private List<Like> sentLikes;

    // etc.
}
```

**In your project:** No @Entity classes! Using stored procedures and manual mapping.

---

## API DESIGN

### RESTful API Contract (What to expect from backend developer)

#### API Versioning Strategy
```
Base URL: https://api.initiate.com/api/v1/

Why v1?
- Allows future breaking changes (v2, v3)
- Mobile app specifies version
- Can run multiple versions simultaneously
```

#### Complete API Endpoints Map

```
====================================================================
AUTHENTICATION APIs (Week 1)
====================================================================

POST   /api/v1/auth/sendotp
Request:  { "mobileNo": "9876543210" }
Response: { "success": true, "message": "OTP sent", "isProfileCompleted": false }
Note: OTP should NOT be in response! (current issue)

POST   /api/v1/auth/verifyotp
Request:  { "mobileNo": "9876543210", "otp": "1234" }
Response: {
    "success": true,
    "token": "eyJhbGc...",        ← OAuth Bearer token
    "userId": 123,
    "isProfileCompleted": false
}
Note: Token valid for 180 days (long-lived session)

POST   /api/v1/auth/logout
Headers: Authorization: Bearer {token}
Request:  {}
Response: { "success": true }

====================================================================
PROFILE APIs (Week 1-2)
====================================================================

GET    /api/v1/profile
Headers: Authorization: Bearer {token}
Response: {
    "userId": 123,
    "fullName": "John Doe",
    "age": 28,
    "gender": "male",
    "bio": "...",
    "profession": "Software Engineer",
    "city": "Mumbai",
    "photos": [
        { "id": 1, "url": "https://s3...", "isProfile": true },
        { "id": 2, "url": "https://s3...", "isProfile": false }
    ],
    "interests": [1, 2, 3],
    "lookingFor": "relationship",
    "isVerified": true,
    "isPremium": false
}

POST   /api/v1/profile/update
Headers: Authorization: Bearer {token}
Request: {
    "fullName": "John Doe",
    "gender": "male",
    "dob": "1995-01-15",
    "bio": "Love traveling...",
    "heightCm": 175,
    "city": "Mumbai",
    "lookingFor": "relationship",
    "latitude": 19.0760,
    "longitude": 72.8777,
    "photos": ["url1", "url2"],
    "interests": [1, 2, 3]
}
Response: { "success": true, "message": "Profile updated" }

POST   /api/v1/profile/photo/upload  (TODO - Week 1)
Headers: Authorization: Bearer {token}
Request: multipart/form-data
    - file: [binary]
    - displayOrder: 1
Response: {
    "success": true,
    "photoId": 789,
    "photoUrl": "https://s3.amazonaws.com/initiate/users/123/photo1.jpg"
}

DELETE /api/v1/profile/photo/{photoId}  (TODO - Week 1)
Headers: Authorization: Bearer {token}
Response: { "success": true }

GET    /api/v1/profile/interests
Response: [
    { "id": 1, "name": "Travel", "description": "Love to explore" },
    { "id": 2, "name": "Music", "description": "Music lover" }
]
Note: Should be cached (static data)

====================================================================
DISCOVERY APIs (Week 2 - HIGH PRIORITY)
====================================================================

GET    /api/v1/discovery/nearby?radiusKm=50
Headers: Authorization: Bearer {token}
Response: [
    {
        "userId": 456,
        "name": "Jane Smith",
        "age": 26,
        "photos": ["url1", "url2"],
        "bio": "Adventure seeker...",
        "profession": "Designer",
        "distanceKm": 3.5,
        "isVerified": true,
        "interests": ["Travel", "Photography"]
    },
    // ... up to 50 profiles
]
Performance: Must return in <2 seconds
Caching: Cache for 10 minutes per user

POST   /api/v1/discovery/swipe
Headers: Authorization: Bearer {token}
Request: {
    "targetUserId": 456,
    "action": "like"  // or "pass"
}
Response: {
    "success": true,
    "isMatch": true,              ← If mutual like
    "matchId": 789,
    "remainingLikes": 24          ← For free users (25/day limit)
}
Business Rules:
- Free users: 25 likes/day (check via Redis counter)
- Premium users: unlimited
- If mutual like: create match + notify both users
- Female-first: No restriction here (applies to messaging)

====================================================================
MATCH APIs (Week 2)
====================================================================

GET    /api/v1/match/list
Headers: Authorization: Bearer {token}
Response: [
    {
        "matchId": 789,
        "matchedUser": {
            "userId": 456,
            "name": "Jane Smith",
            "age": 26,
            "profilePhoto": "url",
            "lastMessage": "Hey! How are you?",
            "lastMessageAt": "2026-01-17T10:30:00Z",
            "unreadCount": 3
        },
        "matchedAt": "2026-01-15T14:20:00Z"
    }
]
Sorting: Order by last message timestamp (most recent first)

GET    /api/v1/match/who-liked-me
Headers: Authorization: Bearer {token}
Response: {
    "count": 12,
    "profiles": [
        // Blurred for free users
        // Full details for premium users
    ],
    "isPremium": false
}

====================================================================
MESSAGE APIs (Week 3)
====================================================================

GET    /api/v1/message/conversations
Headers: Authorization: Bearer {token}
Response: [
    {
        "matchId": 789,
        "otherUser": { "id": 456, "name": "Jane", "photo": "url" },
        "lastMessage": "See you tomorrow!",
        "lastMessageAt": "2026-01-17T10:30:00Z",
        "unreadCount": 2
    }
]

GET    /api/v1/message/chat/{matchId}?page=1&limit=50
Headers: Authorization: Bearer {token}
Response: {
    "messages": [
        {
            "id": 12345,
            "senderId": 123,
            "content": "Hey! How are you?",
            "sentAt": "2026-01-17T10:25:00Z",
            "isRead": true,
            "readAt": "2026-01-17T10:26:00Z"
        }
    ],
    "hasMore": true,
    "totalCount": 245
}
Pagination: 50 messages per page (most recent first)

POST   /api/v1/message/send
Headers: Authorization: Bearer {token}
Request: {
    "matchId": 789,
    "content": "Hi! Nice to meet you!"
}
Response: {
    "success": true,
    "messageId": 12346,
    "sentAt": "2026-01-17T10:30:00Z"
}
Business Rule: Female-first messaging
- If match is new and sender is male: REJECT with error
- If female hasn't sent first message yet: REJECT
- After first female message: Both can message freely

Alternative: Use SignalR Hub instead
Hub.SendMessage(matchId, content)
- Delivers instantly via WebSocket
- No need for polling

====================================================================
VERIFICATION APIs (Week 4)
====================================================================

POST   /api/v1/verification/submit
Headers: Authorization: Bearer {token}
Request: multipart/form-data
    - selfie: [binary]
Response: {
    "success": true,
    "verificationId": 123,
    "status": "pending",
    "estimatedReviewTime": "24 hours"
}

GET    /api/v1/verification/status
Headers: Authorization: Bearer {token}
Response: {
    "status": "approved",  // or "pending", "rejected"
    "submittedAt": "2026-01-16T10:00:00Z",
    "reviewedAt": "2026-01-17T08:00:00Z"
}

====================================================================
ADMIN APIs (For Admin Website - Week 6)
====================================================================

GET    /api/v1/admin/verifications/pending
Headers: Authorization: Bearer {admin-token}
Response: [
    {
        "verificationId": 123,
        "userId": 456,
        "userName": "Jane Smith",
        "profilePhoto": "url",
        "selfieUrl": "url",
        "submittedAt": "2026-01-17T09:00:00Z"
    }
]

POST   /api/v1/admin/verification/approve
Headers: Authorization: Bearer {admin-token}
Request: { "verificationId": 123 }
Response: { "success": true }

POST   /api/v1/admin/verification/reject
Headers: Authorization: Bearer {admin-token}
Request: {
    "verificationId": 123,
    "reason": "Photo doesn't match profile"
}
Response: { "success": true }

GET    /api/v1/admin/users?page=1&limit=50
GET    /api/v1/admin/reports/pending
POST   /api/v1/admin/user/ban
POST   /api/v1/admin/content/delete
```

---

## DEVELOPER MANAGEMENT STRATEGY

### Your Role as Java Architect → PM

You have unique advantage:
- ✅ Understand technical architecture
- ✅ Can review code quality
- ✅ Can spot design issues
- ✅ Can estimate effort accurately
- ❌ Don't know Flutter syntax (but can learn patterns)
- ❌ Don't know C# syntax (but understand concepts)

### How to Review Code Without Knowing Syntax

#### 1. Architecture Review (Your Strength!)

**What to Check:**
```
✅ Is code in right layer?
    - Controllers: Only routing, no business logic
    - BAL: Interfaces and contracts
    - DAL: Database operations only

✅ Are dependencies pointing downward?
    - API → BAL → DAL → Database ✅
    - API → Database directly ❌

✅ Is separation of concerns maintained?
    - Single Responsibility Principle
    - Interface Segregation
    - Dependency Inversion

✅ Are patterns consistent?
    - All controllers follow same structure
    - All services follow same pattern
    - Naming conventions consistent
```

**Questions to Ask Developer:**
- "Show me the folder structure - is it organized by layer or feature?"
- "Where is the business logic for matching algorithm?"
- "How do you handle database transactions?" (should be in stored procedures)
- "What's your exception handling strategy?"

---

#### 2. Code Quality Checkpoints

**Even without knowing syntax, check:**

**A. File Sizes**
```
✅ GOOD:
- Controllers: 50-200 lines each
- Services: 100-300 lines each
- DTOs: 10-50 lines each

❌ RED FLAG:
- Any file > 1000 lines
- Controllers > 500 lines
- Classes doing too much
```

**B. Method Lengths**
```
✅ GOOD: Methods are 10-50 lines
❌ RED FLAG: Methods > 100 lines (needs refactoring)
```

**C. Code Duplication**
```
Ask developer: "I see similar code in 3 places. Can this be extracted to a common method?"
```

**D. Magic Numbers/Strings**
```csharp
// ❌ BAD:
if (user.likesCount >= 25) { ... }        // What's 25?
db.Execute("API_V1_spSomething");          // String literals everywhere

// ✅ GOOD:
if (user.likesCount >= AppConstants.FREE_USER_DAILY_LIKES) { ... }
db.Execute(StoredProcedures.SEND_OTP);
```

---

#### 3. Testing Strategy Enforcement

**Your Managerial Rule:**

> **"No feature is complete without tests"**

**Testing Pyramid for This Project:**

```
        ┌────┐
       ┌┴─E2E┴┐           5% - End-to-end (Flutter + API)
      ┌┴───────┴┐          15% - Integration (API + DB)
     ┌┴─────────┴┐         30% - Widget (Flutter UI)
    ┌┴───────────┴┐        50% - Unit tests (Services)
   ┴───────────────┴
```

**Minimum Testing Requirements (Enforce Weekly):**

**Week 1:**
- [ ] 10 unit tests for AuthService
- [ ] 5 unit tests for ProfileService
- [ ] All tests passing

**Week 2:**
- [ ] 15 unit tests for MatchingService
- [ ] 10 unit tests for DiscoveryService
- [ ] 5 integration tests (API + DB)

**Week 3:**
- [ ] 15 unit tests for MessageService
- [ ] 10 widget tests (Flutter screens)
- [ ] 5 integration tests

**By Launch:**
- [ ] 100+ unit tests
- [ ] 30+ integration tests
- [ ] 20+ widget tests
- [ ] 5+ end-to-end tests

**How to Track:**
```bash
# ASP.NET test results
dotnet test --collect:"XPlat Code Coverage"

# Flutter test results
flutter test --coverage

# Check coverage percentage
# Target: >60% (minimum), >70% (ideal)
```

---

### 4. Weekly Technical Review Checklist

**Every Friday at 4 PM - 1 Hour Review Session**

#### Preparation (30 minutes before meeting)
- [ ] Pull latest code from Git
- [ ] Review commit messages
- [ ] Check test coverage reports
- [ ] List questions

#### Meeting Agenda (60 minutes)

**Part 1: Demo (20 minutes)**
- Backend developer: Demo APIs in Swagger/Postman
- Flutter developer: Demo features on emulator
- **Your rule:** If they can't demo it, it's not done

**Part 2: Code Walkthrough (20 minutes)**
- Pick 1-2 key files
- Developer explains: "Walk me through this code"
- You check: Architecture, separation of concerns, error handling

**Part 3: Technical Discussion (15 minutes)**
```
Questions to ask:
1. "What were the biggest technical challenges this week?"
2. "How did you solve them?"
3. "What did you learn?"
4. "Any tech debt accumulated?"
5. "What's risky for next week?"
```

**Part 4: Plan Next Week (5 minutes)**
- Agree on next week's deliverables
- Identify dependencies
- Commit to specific features

---

### 5. Definition of "Done" (Technical)

**For You to Verify:**

A feature is DONE when ALL these are true:

```
Backend Feature (e.g., "Match Detection"):
✅ Code committed to Git
✅ API endpoint works in Swagger
✅ Stored procedures created
✅ Unit tests written and passing (>70% coverage)
✅ Integration test passing
✅ Error handling implemented
✅ Logging added
✅ Code reviewed
✅ Documentation updated
✅ Performance acceptable (<2s response time)
✅ Security validated (no SQL injection, auth required)

Flutter Feature (e.g., "Swipe UI"):
✅ Code committed to Git
✅ Works on Android emulator
✅ Works on iOS simulator (if testing both)
✅ Unit tests for business logic
✅ Widget tests for UI
✅ Handles loading state
✅ Handles error state
✅ Works offline gracefully
✅ Animations smooth (60 FPS)
✅ Integrates with backend API
```

**Common Developer Excuses to Reject:**
- ❌ "It works on my machine" → Not done until tested on multiple devices
- ❌ "I'll add tests later" → Tests are part of feature, not optional
- ❌ "Just needs small fix" → Fix it, then it's done
- ❌ "Almost done, 90% complete" → Either done or not done, no percentages

---

## WEEK-BY-WEEK TECHNICAL DELIVERABLES

### Week 1 (Jan 6-10) - Foundation [LATE BUT CRITICAL]

#### Backend Developer Deliverables

**Must Have (P0):**
- [x] 3-layer project structure ✅ (DONE)
- [ ] MySQL connection working
- [ ] 5 core tables created:
  - [ ] users
  - [ ] user_photos
  - [ ] likes
  - [ ] matches
  - [ ] SexualInterestMaster ✅ (DONE)
- [ ] 10 stored procedures:
  - [x] API_V1_spSexualInterest ✅ (DONE)
  - [ ] API_V1_spSendOtp
  - [ ] API_V1_spVerifyOtp
  - [ ] API_V1_spGetUserProfile
  - [ ] API_V1_spUpdateUserProfile
  - [ ] API_V1_spUploadPhoto
  - [ ] API_V1_spDeletePhoto
  - [ ] API_V1_spGetInterests
  - [ ] API_V1_spSaveUserLocation
  - [ ] API_V1_spGetUserById
- [ ] Authentication working end-to-end
- [ ] Profile CRUD working
- [ ] Photo upload to S3 working
- [ ] 10 unit tests written
- [ ] Swagger documentation updated
- [ ] All 5 security issues fixed:
  - [ ] OTP not in response
  - [ ] Input validation added
  - [ ] Rate limiting on OTP
  - [ ] Credentials in secrets
  - [ ] HTTPS enforced

**Technical Debt Allowed:**
- ⚠️ No caching yet (add Week 2)
- ⚠️ No background jobs yet (add Week 3)

#### Flutter Developer Deliverables

**Must Have (P0):**
- [ ] Flutter project initialized
  - [ ] Clean architecture folders
  - [ ] Riverpod state management setup
  - [ ] Dio HTTP client configured
- [ ] Authentication screens:
  - [ ] Login screen UI
  - [ ] OTP verification screen UI
  - [ ] Profile creation wizard (3 steps)
- [ ] API integration:
  - [ ] Auth service (sendOtp, verifyOtp)
  - [ ] Token storage (SecureStorage)
  - [ ] API interceptor for auth header
- [ ] Basic navigation:
  - [ ] Router setup (go_router)
  - [ ] Auth state management
  - [ ] Protected routes
- [ ] 5 widget tests
- [ ] App runs on Android emulator

**Technical Specs:**
```dart
// Folder structure you should see:
lib/
├── core/
│   ├── constants/
│   │   └── api_constants.dart        ← API URLs
│   ├── theme/
│   │   └── app_theme.dart           ← Colors, fonts
│   └── utils/
├── data/
│   ├── models/
│   │   └── user_model.dart          ← Data models
│   ├── repositories/
│   │   └── auth_repository.dart     ← API calls
│   └── datasources/
│       └── auth_remote_datasource.dart
├── domain/
│   ├── entities/
│   │   └── user.dart                ← Business entities
│   └── usecases/
│       └── login_usecase.dart       ← Business logic
├── presentation/
│   ├── screens/
│   │   └── auth/
│   │       ├── login_screen.dart
│   │       └── otp_screen.dart
│   └── providers/
│       └── auth_provider.dart       ← State management
└── main.dart
```

---

### Week 2 (Jan 13-17) - Discovery & Matching [CURRENT WEEK]

#### Backend Technical Deliverables

**Stored Procedures (Critical!):**
```sql
-- Priority 1: Proximity search
API_V1_spGetNearbyUsers(
    @UserId INT,
    @Latitude DECIMAL(10,7),
    @Longitude DECIMAL(10,7),
    @RadiusKm INT
)
Returns: Users within radius, excluding already liked, ordered by match score

-- Priority 2: Swipe action
API_V1_spProcessSwipe(
    @FromUserId INT,
    @ToUserId INT,
    @IsLike BIT,
    @IsMatch BIT OUTPUT          -- Returns if it's a match
)
Returns: Success, IsMatch flag, MatchId (if match)
Logic:
1. INSERT into likes
2. Check daily limit (query Redis or DB counter)
3. SELECT reciprocal like
4. IF reciprocal: INSERT into matches
5. Return match status

-- Priority 3: Get matches
API_V1_spGetUserMatches(@UserId INT)
Returns: All matches with last message info

-- Priority 4: Who liked me (for premium feature)
API_V1_spWhoLikedMe(@UserId INT, @IsPremium BIT)
Returns: Blurred profiles for free, full for premium
```

**Technical Challenges to Discuss:**
- **Spatial Query Performance:** Ensure ST_Distance_Sphere is indexed
- **Daily Limit Counter:** Use Redis or DB? (Recommend Redis)
- **Transaction Management:** Ensure atomicity for match creation

#### Flutter Technical Deliverables

**Swipe Card Component:**
```dart
// Key technical requirements:
class SwipeCard extends StatefulWidget {
  final UserProfile profile;
  final Function(SwipeDirection) onSwipe;

  // Must support:
  // - Drag gesture
  // - Rotation during drag
  // - Snap to side or return
  // - Smooth animation (60 FPS)
}
```

**State Management:**
```dart
// Discovery state provider
final discoveryProvider = StateNotifierProvider<DiscoveryNotifier, DiscoveryState>((ref) {
  return DiscoveryNotifier(ref.read(discoveryRepositoryProvider));
});

// State should track:
// - Current profiles list
// - Loading state
// - Error state
// - Remaining daily likes (for free users)
// - Match status (show dialog)
```

**Performance Requirements:**
- Load 50 profiles in background
- Preload next 3 images
- Cache images (cached_network_image package)
- Smooth animations (use AnimatedContainer, Hero)

---

### Week 3 (Jan 20-24) - Messaging

#### Backend: SignalR Hub

**Java Equivalent:** WebSocket implementation

```csharp
// ChatHub.cs
[Authorize]
public class ChatHub : Hub
{
    // OnConnectedAsync: User joins their personal "room"
    // SendMessage: Broadcast to receiver's room
    // MarkAsRead: Update DB + notify sender
    // TypingIndicator: Real-time typing status
}
```

**Technical Requirements:**
- Token-based authentication for WebSocket
- Group management (one group per user)
- Message persistence (save to DB before broadcasting)
- Offline message queuing

#### Flutter: SignalR Client

```dart
// signalr_core package
class ChatService {
  HubConnection _hubConnection;

  // Connect with JWT token
  // Listen for: ReceiveMessage, MessageRead, UserTyping
  // Send via: SendMessage, MarkAsRead, TypingIndicator
}
```

---

### Week 4 (Jan 27-31) - Verification & Premium

**AWS Rekognition Integration:**
```csharp
// FacialRecognitionService.cs
public class FacialRecognitionService
{
    // DetectFaces: Check if photo has exactly 1 face
    // CompareFaces: Compare selfie with profile photo
    // Return confidence score
}
```

**Payment Gateway (Mock for MVP):**
```csharp
// PaymentService.cs
public class PaymentService
{
    // For MVP: Just return success (mock)
    // Phase 2: Integrate Razorpay/Stripe
    public PaymentResult ProcessPayment(PaymentRequest request)
    {
        // Mock implementation
        return new PaymentResult { Success = true };
    }
}
```

---

### Week 5-6 (Feb 3-14) - Polish, Testing, Deployment

**Load Testing (Your responsibility):**
```bash
# Use Apache JMeter (you know this from Java!)
# Test scenarios:
- 100 concurrent users login
- 500 swipes per minute
- 1000 messages per minute
- Database query performance

# Acceptance criteria:
- API p95 response time: <2s
- API p99 response time: <5s
- Database CPU: <70%
- Zero crashes under load
```

---

## ADMIN WEBSITE INTEGRATION

### Admin Portal Architecture

```
┌─────────────────────────────────────────────────────────┐
│              ADMIN WEB PORTAL                           │
│            (ASP.NET MVC - Same Solution)                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Controllers/                                           │
│  ├── AdminAuthController        ← Admin login          │
│  ├── AdminDashboardController   ← Stats & metrics      │
│  ├── VerificationController     ← Manual verification  │
│  ├── ModerationController       ← Handle reports       │
│  └── UserManagementController   ← Ban/unban users      │
│                                                         │
│  Views/ (Razor pages)                                   │
│  ├── Auth/Login.cshtml                                  │
│  ├── Dashboard/Index.cshtml     ← Charts & stats       │
│  ├── Verification/Pending.cshtml← Approve selfies      │
│  ├── Moderation/Reports.cshtml  ← User reports         │
│  └── Users/List.cshtml           ← User management     │
│                                                         │
└────────────────────┬────────────────────────────────────┘
                     │
                     │ Uses same backend services
                     │
            ┌────────▼────────┐
            │  initiate.BAL   │  ← Shared business layer
            │  initiate.DAL   │  ← Shared data access
            └─────────────────┘
                     │
            ┌────────▼────────┐
            │  MySQL Database │  ← Same database
            └─────────────────┘
```

### Admin Features (Week 6 - Quick Build)

**Priority Features:**
1. **Verification Queue**
   - List pending selfies
   - Side-by-side: Profile photo vs Selfie
   - Approve/Reject buttons
   - Rejection reasons

2. **User Management**
   - Search users
   - View user details
   - Ban/unban user
   - Delete account (GDPR)

3. **Reports Dashboard**
   - Pending reports
   - User report history
   - Take action: warn, ban, delete content

4. **Analytics Dashboard**
   - Daily active users
   - New registrations
   - Matches created
   - Messages sent
   - Premium conversions
   - Revenue (when payments integrated)

**Tech Stack:**
- ASP.NET MVC (same solution, different project)
- Bootstrap 5 for UI
- Chart.js for analytics
- Same DAL services (reuse all stored procedures!)

**Development Time:** 3-4 days (Week 6)
- Day 1: Authentication + Dashboard
- Day 2: Verification queue
- Day 3: Moderation + User management
- Day 4: Testing + Polish

---

## RISK MANAGEMENT & TECHNICAL DEBT

### Technical Risks (Your Java Background Helps Here!)

#### Risk #1: Stored Procedures Over-Engineering

**Problem:** Developer might put too much logic in stored procedures

**What to watch for:**
```sql
-- ❌ RED FLAG: Complex business logic in SQL
CREATE PROCEDURE API_V1_spProcessSwipe AS
BEGIN
    -- 200 lines of complex SQL
    -- Multiple IF/ELSE
    -- Cursors and loops
    -- Complex calculations
END

-- ✅ GOOD: SQL does data operations, C# does logic
CREATE PROCEDURE API_V1_spCreateLike AS
BEGIN
    INSERT INTO likes (from_user_id, to_user_id, liked_at)
    VALUES (@FromUserId, @ToUserId, GETDATE());

    SELECT SCOPE_IDENTITY() AS LikeId;
END

-- C# handles: limit checking, match detection, notifications
```

**Your Action:** Review stored procedures weekly. If any procedure > 100 lines, discuss with developer.

---

#### Risk #2: No Database Migration Strategy

**Java Equivalent:** Flyway/Liquibase for version control

**Problem:** Schema changes need to be tracked

**Solution:** Require developer to:
```
SQL/
├── migrations/
│   ├── V001__CreateUsersTable.sql
│   ├── V002__CreatePhotosTable.sql
│   ├── V003__CreateLikesTable.sql
│   └── ...
└── rollback/
    ├── R001__DropUsersTable.sql
    └── ...
```

**Each migration script:**
- Numbered (V001, V002, etc.)
- Idempotent (can run multiple times safely)
- Has rollback script

---

#### Risk #3: SignalR Connection Management

**Problem:** WebSocket connections can drop, need reconnection

**What to verify:**
```csharp
// C# (Backend)
services.AddSignalR(options =>
{
    options.KeepAliveInterval = TimeSpan.FromSeconds(15);  // ✅ Required
    options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);  // ✅ Required
});
```

```dart
// Flutter (Frontend)
HubConnectionBuilder()
    .withUrl(url)
    .withAutomaticReconnect()  // ✅ Critical!
    .build();
```

**Test Scenario (You should test this):**
- Start chat
- Turn off Wi-Fi for 10 seconds
- Turn on Wi-Fi
- **Expected:** Chat reconnects automatically, messages sync

---

#### Risk #4: Photo Upload Performance

**Problem:** Uploading 6 photos can take 30+ seconds

**Solution Architecture:**
```
User selects photos
    ↓
Flutter: Compress locally (image_picker, flutter_image_compress)
    - Original: 3MB each
    - Compressed: <500KB each
    ↓
Upload one by one (or parallel)
    ↓
S3 Direct Upload (presigned URLs)
    - Backend generates presigned URL
    - Flutter uploads directly to S3 (bypasses API)
    - Faster and doesn't load API server
    ↓
Send S3 URLs to backend
    - Backend saves URLs in database
```

**Performance Target:**
- 6 photos upload in <10 seconds total
- Progress indicator shows each upload
- Can proceed even if some uploads fail

---

### Technical Debt Tracking

**Create a TECH_DEBT.md file:**

```markdown
# Technical Debt Log

## Week 1 Debt
- [ ] No caching layer (Redis) - Add Week 2
- [ ] No rate limiting - Add Week 2
- [ ] Manual DataReader mapping - Consider Dapper Week 3
- [ ] Global lock in MySqlUtilityDb - Refactor Week 3

## Week 2 Debt
- [ ] No background job for daily like reset - Add Week 3
- [ ] Discovery results not cached - Add Week 3

## Week 3 Debt
- [ ] Message polling instead of SignalR - Migrate Week 4
```

**Weekly Review:** Decide what debt to pay down vs. what can wait

---

## PRODUCTION DEPLOYMENT ARCHITECTURE

### Deployment Topology

```
┌─────────────────────────────────────────────────────────────────┐
│                          USERS                                  │
│                    (Mobile App Users)                           │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           │ HTTPS (SSL/TLS)
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│                   LOAD BALANCER                                 │
│                (AWS ALB / Azure Load Balancer)                  │
│                                                                 │
│  - SSL Termination                                             │
│  - Health checks                                               │
│  - Auto-scaling trigger                                        │
└─────────┬─────────────────────────────┬─────────────────────────┘
          │                             │
          │                             │
┌─────────▼──────────┐         ┌────────▼─────────┐
│   Web Server 1     │         │   Web Server 2   │
│  (IIS / Windows)   │         │  (IIS / Windows) │
│                    │         │                  │
│  ASP.NET 4.7.2 API │         │  ASP.NET 4.7.2   │
│  + SignalR Hub     │         │  + SignalR Hub   │
│                    │         │                  │
│  - Auth APIs       │         │  - Auth APIs     │
│  - Profile APIs    │         │  - Profile APIs  │
│  - Discovery APIs  │         │  - Discovery APIs│
│  - Message APIs    │         │  - Message APIs  │
└─────────┬──────────┘         └────────┬─────────┘
          │                             │
          │   Connection Pool           │
          │                             │
┌─────────▼─────────────────────────────▼─────────┐
│          MySQL Database Cluster                 │
│                                                  │
│  ┌────────────────┐      ┌────────────────┐    │
│  │  Primary (RW)  │──────│  Replica (RO)  │    │
│  │                │ Repl │                │    │
│  └────────────────┘      └────────────────┘    │
│                                                  │
│  - All tables                                   │
│  - All stored procedures                        │
│  - Automated backups (daily)                    │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│          Redis Cache Cluster                     │
│                                                  │
│  - Session tokens                               │
│  - Daily like counters                          │
│  - Discovery feed cache                         │
│  - Lookup data cache                            │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│          AWS S3 Buckets                          │
│                                                  │
│  Bucket: initiate-profile-photos                │
│  - User uploaded photos                         │
│  - Thumbnail generation (Lambda)                │
│  - CloudFront CDN in front                      │
│                                                  │
│  Bucket: initiate-verification-photos           │
│  - Selfie verification photos                   │
│  - Private (not public)                         │
└──────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────┐
│          External Services                       │
│                                                  │
│  - Twilio (SMS/OTP)                             │
│  - SendGrid (Email)                             │
│  - AWS Rekognition (Face detection)             │
│  - Razorpay (Payments - Phase 2)                │
└──────────────────────────────────────────────────┘
```

### Deployment Checklist (Week 6)

**Environment Setup:**
```
Development:
- Local IIS Express
- Local MySQL
- Local Redis (optional)
- Mock external services

Staging:
- Windows Server (AWS EC2 or Azure VM)
- MySQL RDS (dev tier)
- Redis Cloud (free tier)
- Real external services (test mode)

Production:
- Windows Server (2 instances behind load balancer)
- MySQL RDS (multi-AZ, automated backups)
- Redis Cluster (3 nodes)
- Real external services (production mode)
- CloudFront CDN for images
```

---

## COORDINATION STRATEGY

### Backend + Flutter Sync Points

#### API Contract First (Critical!)

**Week 1 Monday Morning:**
Backend developer must publish API contracts in Swagger BEFORE writing code.

**Example:**
```yaml
# swagger.yaml (or document in Confluence/Notion)

/api/v1/discovery/nearby:
  get:
    summary: Get nearby user profiles
    parameters:
      - name: radiusKm
        in: query
        type: integer
        default: 50
    responses:
      200:
        schema:
          type: array
          items:
            $ref: '#/definitions/UserProfile'
```

**Flutter developer:**
- Can start UI immediately
- Uses mock data matching contract
- Integrates real API when backend ready

**Your tool:** API contract review session every Monday (30 min)

---

#### Daily Standup Protocol

**Time:** 9:30 AM (15 minutes max)

**Format:**
```
Backend Developer (5 min):
✅ Yesterday: "Completed match detection stored procedure"
🔨 Today: "Working on message API and SignalR hub"
🚧 Blockers: "Need AWS S3 bucket credentials"
📊 Metrics: "Test coverage: 65%, API response time: 850ms"

Flutter Developer (5 min):
✅ Yesterday: "Completed swipe animation and API integration"
🔨 Today: "Working on match dialog and chat screen"
🚧 Blockers: "Waiting for message API to be deployed"
📊 Metrics: "Test coverage: 55%, App build size: 18MB"

You (PM) (5 min):
- Address blockers immediately
- Clarify any dependencies
- Make quick decisions
- Set focus for the day
```

---

#### Integration Testing Schedule

**Every Wednesday (1 hour):**
- Backend deploys to staging
- Flutter developer integrates with staging APIs
- Test end-to-end flows together
- Document issues
- Fix within 24 hours

---

### Dependency Management

**Critical Dependencies to Track:**

```
Week 1:
Backend → MySQL setup → Backend can develop
Backend → Auth APIs → Flutter can integrate

Week 2:
Backend → Discovery API → Flutter can build swipe UI
Backend → Swipe API → Flutter can test matching

Week 3:
Backend → SignalR hub → Flutter can implement real-time chat
Flutter → Chat UI → Backend can test message flow

Week 4:
Backend → Verification API → Flutter can submit selfies
Backend → AWS S3 setup → Flutter can upload photos

Week 5:
Backend → Subscription API → Flutter can build premium flow
Flutter → Payment UI → Backend can test payment flow

Week 6:
Both → Staging environment → Can do integration testing
Both → Production environment → Can do final testing
```

**Use Gantt Chart (I'll create for you below)**

---

## QUALITY GATES

### Gate 1: Week 1 (Security & Foundation)

**Criteria to pass:**
- [ ] All 5 security issues fixed
- [ ] Authentication working end-to-end
- [ ] 10+ unit tests passing
- [ ] Code in Git with daily commits
- [ ] Swagger documentation complete

**If fail:** DO NOT proceed to Week 2. Fix issues first.

---

### Gate 2: Week 2 (Core Features)

**Criteria to pass:**
- [ ] User can swipe on profiles (Flutter + Backend)
- [ ] Matches are created correctly
- [ ] Discovery API returns in <2s
- [ ] 25+ unit tests passing
- [ ] Flutter app running on real device

**If fail:** Extend Week 2, don't start Week 3 messaging.

---

### Gate 3: Week 3 (Messaging)

**Criteria to pass:**
- [ ] Real-time chat working (SignalR)
- [ ] Messages persist to database
- [ ] Female-first rule enforced
- [ ] 40+ unit tests passing
- [ ] Load test: 100 concurrent users

**If fail:** Messaging is MVP-critical. Must fix before proceeding.

---

### Gate 4: Week 4 (Verification)

**Criteria to pass:**
- [ ] Selfie verification flow working
- [ ] AWS Rekognition integrated
- [ ] Manual approval queue working (admin portal)
- [ ] 50+ unit tests passing

**Can compromise:** Manual verification only for MVP (skip AWS Rekognition)

---

### Gate 5: Week 5 (Polish)

**Criteria to pass:**
- [ ] All MVP features working
- [ ] <5 known bugs (all low priority)
- [ ] 70+ unit tests passing
- [ ] Performance acceptable (all APIs <3s)
- [ ] Security scan passed

**Final GO/NO-GO decision:** End of Week 5

---

### Gate 6: Week 6 (Launch Prep)

**Criteria to pass:**
- [ ] Deployed to production
- [ ] All smoke tests passing
- [ ] 100 UAT test cases passed
- [ ] Rollback plan tested
- [ ] Monitoring and alerts configured

**If pass:** Launch!
**If fail:** Delay launch, fix critical issues

---

## TECHNICAL METRICS DASHBOARD

### What to Track Daily (Use Excel/Google Sheets)

```
| Date | Backend Progress | Flutter Progress | Tests | Issues | Notes |
|------|------------------|------------------|-------|--------|-------|
| Jan 17 | 5% (1 API done) | 0% (not started) | 0 | 5 critical | Behind |
| Jan 18 | | | | | |
| Jan 19 | | | | | |
...
```

### Code Quality Metrics (Weekly)

```
Week 1 Report:
├── Backend
│   ├── Lines of Code: 3,500
│   ├── Test Coverage: 0% → Target: 50%
│   ├── API Endpoints: 2/30 complete
│   ├── Stored Procedures: 1/50 complete
│   └── Critical Issues: 5 open
├── Flutter
│   ├── Lines of Code: 0
│   ├── Test Coverage: N/A
│   ├── Screens: 0/15 complete
│   └── Critical Issues: App not started
└── Overall Health: 🔴 RED (behind schedule)
```

### API Performance Metrics (Track from Week 2)

```
Endpoint Performance (p95):
├── POST /auth/sendotp: ___ms (target: <500ms)
├── POST /auth/verifyotp: ___ms (target: <500ms)
├── GET /profile: ___ms (target: <1000ms)
├── GET /discovery/nearby: ___ms (target: <2000ms)
├── POST /discovery/swipe: ___ms (target: <500ms)
└── POST /message/send: ___ms (target: <300ms)
```

**Tool:** Use Postman + Newman for automated API testing

---

## TECHNICAL REVIEW TEMPLATE

### Code Review Template (For Your Use)

**When reviewing backend code:**

```
File: ProfileService.cs
Reviewed By: Bikash Kumar
Date: Jan 17, 2026

Architecture (Score: __/10)
✅ Is code in correct layer? (BAL vs DAL)
✅ Follows single responsibility?
✅ Dependencies injected properly?

Code Quality (Score: __/10)
✅ Methods < 50 lines?
✅ Clear variable names?
✅ No code duplication?
✅ Error handling present?

Security (Score: __/10)
✅ Input validation?
✅ SQL injection prevented?
✅ No sensitive data logged?

Performance (Score: __/10)
✅ Queries optimized?
✅ Caching used where appropriate?
✅ No N+1 queries?

Testing (Score: __/10)
✅ Unit tests present?
✅ Test coverage >70%?
✅ All tests passing?

Overall: __/50
Status: ✅ Approved / ⚠️ Needs minor fixes / ❌ Needs major rework

Action Items:
1.
2.
3.
```

---

## EMERGENCY PROTOCOLS

### Scenario 1: Backend is Blocking Flutter

**Symptoms:**
- Flutter developer can't make progress
- Waiting for API to be ready

**Your Action (Within 2 hours):**
1. Backend developer: Publish mock API endpoint
2. Flutter developer: Use mock data and continue
3. Integration happens when backend ready

**Mock API Tool:**
```json
// Use JSON Server or Mockoon
// mock-api.json
{
  "/api/v1/profile": {
    "userId": 123,
    "name": "Test User",
    ...
  }
}
```

---

### Scenario 2: Critical Bug Found on Friday

**Decision Matrix:**

**If P0 (App crashes):**
- ⛔ Stop all new development
- 🔥 All hands on fixing bug
- 🚫 Do NOT deploy to production
- 📅 May need to work weekend

**If P1 (Feature broken):**
- ⚠️ Create hotfix branch
- 👤 One developer fixes, other continues
- ✅ Fix by Monday

**If P2 (Minor issue):**
- 📝 Add to backlog
- ➡️ Fix next sprint

---

### Scenario 3: Developer Wants to Change Architecture

**Example:** "I want to switch from stored procedures to Entity Framework"

**Your Response Process:**
1. **Listen:** "Explain your reasoning"
2. **Assess:** "What's the cost/benefit?"
3. **Impact:** "How many days will this take?"
4. **Timeline:** "Will we still make deadline?"
5. **Decide:**
   - If <2 days + significant benefit: ✅ Approve
   - If >3 days or questionable benefit: ❌ Reject
   - If critical for success: ⚠️ Escalate

**Document Decision:**
```
Architecture Decision Record (ADR)

Date: Jan 17, 2026
Decision: Keep stored procedures (reject EF switch)
Context: Developer wants to switch to Entity Framework
Reasoning:
  - Already 1 SP implemented
  - Would take 3-5 days to migrate
  - Timeline is tight
  - SPs can achieve required performance
Decision: Continue with SPs, revisit in Phase 2
Stakeholders: Bikash (PM), Abhishek (Backend Dev)
```

---

## ADMIN WEBSITE TECHNICAL SPECS

### Quick Implementation Plan (Week 6)

**Tech Stack (Reuse Same Solution):**
```
InitiateProject.sln
├── initiate.API         (Existing - Mobile APIs)
├── initiate.BAL         (Shared - Business layer)
├── initiate.DAL         (Shared - Data access)
└── initiate.AdminWeb    (NEW - Admin portal)
    ├── Controllers/
    │   ├── AdminAuthController
    │   ├── DashboardController
    │   ├── VerificationController
    │   └── ModerationController
    ├── Views/
    │   ├── Auth/Login.cshtml
    │   ├── Dashboard/Index.cshtml
    │   ├── Verification/Queue.cshtml
    │   └── Moderation/Reports.cshtml
    └── wwwroot/
        ├── css/bootstrap.min.css
        └── js/chart.min.js
```

**Reuse Everything:**
- ✅ Same DAL services (AuthService, ProfileService)
- ✅ Same stored procedures
- ✅ Same database
- ✅ Same business logic

**Only New Code:**
- Admin-specific controllers (5-6 controllers)
- Razor views (HTML + C#)
- Basic authentication (separate admin login)

**Effort:** 3 days maximum

---

### Admin Features Priority

**Must Have (P0):**
1. Login (admin credentials)
2. Dashboard with key metrics
3. Verification queue (approve/reject selfies)
4. User search and view

**Should Have (P1):**
5. Reports moderation
6. User ban/unban
7. Content deletion

**Nice to Have (P2):**
8. Analytics charts
9. Bulk operations
10. Export data (CSV)

**Timeline:**
- Day 1: Authentication + Dashboard
- Day 2: Verification queue
- Day 3: Moderation + User management
- Day 4: Polish + Testing

---

## JAVA DEVELOPER TIPS FOR MANAGING .NET/FLUTTER

### 1. Reading C# Code (Quick Guide)

**Your Java Knowledge Translates:**

| Java | C# | Notes |
|------|-----|-------|
| `public class User` | `public class User` | Same |
| `@Autowired` | Constructor injection | Different syntax, same concept |
| `@Service` | `[Service]` or Unity registration | Different mechanism |
| `List<User>` | `List<User>` | Same generic syntax! |
| `user.getName()` | `user.Name` | Properties, not getters |
| `throw new Exception()` | `throw new Exception()` | Same |
| `try-catch-finally` | `try-catch-finally` | Same |
| `implements UserRepository` | `: IUserRepository` | Colon instead of implements |

**You can read C# code!** Syntax is 80% similar to Java.

---

### 2. Reading Flutter/Dart Code (Basics)

**Dart is similar to Java (both object-oriented):**

```dart
// Dart (Flutter)
class UserProfile {
  final int id;
  final String name;
  final int age;

  UserProfile({required this.id, required this.name, required this.age});
}

// Java equivalent:
class UserProfile {
    private final int id;
    private final String name;
    private final int age;

    public UserProfile(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}
```

**Key Differences:**
- `final` instead of `private final`
- No semicolons needed (but allowed)
- Widgets instead of Views (UI)
- `async`/`await` like JavaScript (not Java Futures)

**You can understand Flutter code structure!**

---

### 3. Architecture Review (Your Expertise!)

**Use Your Java Architecture Knowledge:**

**SOLID Principles (Universal!):**
- **S**ingle Responsibility: Each class one job
- **O**pen/Closed: Open for extension, closed for modification
- **L**iskov Substitution: Subtypes should be replaceable
- **I**nterface Segregation: Many specific interfaces > one general
- **D**ependency Inversion: Depend on abstractions, not concretions

**Check in code reviews:**
- ✅ Are services doing one thing?
- ✅ Are there interfaces for repositories?
- ✅ Can you swap implementations easily?
- ✅ Are dependencies injected?

**Design Patterns (Same in all languages!):**
- Repository Pattern ✅ (being used)
- Factory Pattern (for creating DTOs)
- Strategy Pattern (for matching algorithm)
- Observer Pattern (for notifications)
- Singleton Pattern (MySqlUtilityDb)

---

## FINAL DELIVERY CHECKLIST

### Launch Readiness (Your Final Sign-Off)

**Technical Criteria:**
- [ ] All APIs responding in <3 seconds
- [ ] Flutter app installs and runs
- [ ] Can register, create profile, swipe, match, chat (full flow)
- [ ] 70+ tests passing (50 backend, 20 Flutter)
- [ ] Security scan passed (OWASP top 10)
- [ ] Load test passed (100 concurrent users)
- [ ] Database backups configured
- [ ] Monitoring and alerts configured
- [ ] Rollback plan documented and tested
- [ ] All 🔴 critical issues resolved
- [ ] <5 known bugs (all low priority)

**Managerial Criteria:**
- [ ] Team confident in stability
- [ ] Stakeholders approved
- [ ] Support plan in place
- [ ] Post-launch plan ready

---

## SUMMARY FOR YOU

### Your Strengths as Java Architect Managing This Project

✅ **Architecture Understanding:** You know 3-tier, SOA, design patterns
✅ **Code Quality:** You can spot bad design across languages
✅ **Database Design:** MySQL is similar to Oracle/PostgreSQL you know
✅ **Performance:** You understand caching, indexing, optimization
✅ **Testing:** JUnit concepts apply to xUnit/Flutter tests

### Your Learning Curve

**Minimal (1-2 days):**
- C# syntax (80% similar to Java)
- ASP.NET Web API basics
- Dart/Flutter basics (just enough to review)

**Resources:**
- "C# for Java Developers" (Microsoft docs) - 2 hours
- "Flutter for Web Developers" (Flutter.dev) - 2 hours

---

## RECOMMENDED WEEKLY SCHEDULE

**Monday:**
- 9:30 AM: Standup (15 min)
- 10:00 AM: API contract review (30 min)
- Rest of day: Developers work independently

**Tuesday:**
- 9:30 AM: Standup (15 min)
- Rest of day: Developers work independently

**Wednesday:**
- 9:30 AM: Standup (15 min)
- 2:00 PM: Integration testing session (1 hour)
- Rest of day: Bug fixing from integration test

**Thursday:**
- 9:30 AM: Standup (15 min)
- Rest of day: Developers work independently

**Friday:**
- 9:30 AM: Standup (15 min)
- 4:00 PM: Weekly review + demo (1 hour)
  - Review metrics
  - Demo features
  - Discuss next week
  - Quality gate checkpoint

---

## CONCLUSION

You have **strong technical foundation** (Java/architecture) to manage this project effectively. Key points:

1. **Architecture:** You understand the 3-layer design
2. **Patterns:** Same design patterns across languages
3. **Code Review:** Focus on structure, not syntax
4. **Quality Gates:** Enforce testing and standards
5. **Risk Management:** Identify technical debt early
6. **Coordination:** API contracts first, integration weekly
7. **Admin Portal:** Quick 3-day build in Week 6

**Your Success Formula:**
- 🎯 Clear weekly deliverables
- 📊 Track metrics (tests, coverage, performance)
- 🚧 Unblock developers fast
- ✅ Enforce quality gates
- 📱 Keep mobile + backend in sync
- 🔒 Never compromise on security

**You've got this!** 💪🚀

---

**END OF PROJECT MANAGER TECHNICAL GUIDE**

Next Steps: Use this as your daily reference for managing both developers.
