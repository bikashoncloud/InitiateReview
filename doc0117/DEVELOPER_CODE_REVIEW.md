# Initiate Dating App - Code Review for Developer

**Review Date:** January 17, 2026
**Developer:** Abhishek
**Reviewer:** Technical Review Team
**Priority:** Action Required

---

## SUMMARY

Good progress on architecture! The 3-layer design with Unity DI and AutoMapper is well-structured. However, there are **critical security issues** and **important architectural decisions** that need immediate attention.

**Overall Score:** 5/10 - Needs Improvement

---

## 🔴 CRITICAL ISSUES (Fix Immediately)

### 1. OTP Security Breach
**File:** `initiate.DAL/AuthService.cs:41`

```csharp
// ❌ CURRENT (SECURITY RISK):
response.Otp = reader["Otp"].ToString();

// ✅ FIX:
// Remove this line completely. OTP should NEVER be in API response.
// OTP should only be sent via SMS/Email.
```

**Why this is critical:** Anyone can see the OTP in network traffic, defeating its purpose.

---

### 2. Duplicate API Projects
**Issue:** Two API projects in solution:
- `initiate.API`
- `initiateProject`

**Action:** Please clarify which is active and remove the other to avoid confusion.

---

### 4. Missing Input Validation
**File:** `initiate.DAL/AuthService.cs`

```csharp
// ❌ CURRENT (No validation):
public vm_userLogin sendOtp(string MobileNo)
{
    // Directly calls stored procedure
}

// ✅ ADD VALIDATION:
public vm_userLogin sendOtp(string MobileNo)
{
    // Validate mobile number format
    if (string.IsNullOrWhiteSpace(MobileNo))
        return Error("Mobile number is required");

    if (!Regex.IsMatch(MobileNo, @"^[6-9]\d{9}$"))
        return Error("Invalid Indian mobile number");

    // Check rate limit (max 3 OTPs per hour per number)
    if (IsRateLimited(MobileNo))
        return Error("Too many OTP requests. Please try after 1 hour");

    // Proceed with OTP
}
```

---

### 5. No Exception Logging
**Current:** Exceptions are caught but not logged

```csharp
// ❌ CURRENT:
catch (Exception ex)
{
    response.isSuccess = false;
    response.ResponseMessage = "An error occurred";
}

// ✅ FIX - Add Serilog:
catch (Exception ex)
{
    _logger.LogError(ex, "Error in sendOtp for MobileNo: {MobileNo}", MobileNo);
    response.isSuccess = false;
    response.ResponseMessage = "An error occurred. Please try again";
}
```

**Install:**
```bash
Install-Package Serilog.AspNetCore
Install-Package Serilog.Sinks.File
```

---

## 🟡 HIGH PRIORITY (Fix This Week)

### 6. Global Lock Bottleneck
**File:** `initiate.DAL/Setting/MySqlUtilityDb.cs`

```csharp
// ❌ CURRENT (Serializes ALL database operations):
lock (_lock)
{
    // Database operation
}

// ✅ FIX (Use connection pooling):
// Remove global lock
// Let MySQL connection pooling handle concurrency
// Configure in connection string:
// "Min Pool Size=5;Max Pool Size=100;"
```

**Why:** Global lock will cause performance issues under load. All API requests will queue.

---

### 7. No Caching for Static Data
**File:** `initiate.DAL/ProfileService.cs`

```csharp
// ❌ CURRENT (Hits database every time):
public List<SexualInterestResDTO> GetSexualInterestList()
{
    var rdr = _db.fn_DataReader("API_V1_spSexualInterest");
    // ...
}

// ✅ FIX (Add memory cache):
private static List<SexualInterestResDTO> _cachedInterests;
private static DateTime _cacheExpiry;

public List<SexualInterestResDTO> GetSexualInterestList()
{
    if (_cachedInterests != null && DateTime.Now < _cacheExpiry)
        return _cachedInterests;

    var rdr = _db.fn_DataReader("API_V1_spSexualInterest");
    // ... populate list

    _cachedInterests = list;
    _cacheExpiry = DateTime.Now.AddHours(24);
    return list;
}
```

---

### 8. Manual DataReader Mapping
**Current:** Manually mapping DataReader to DTOs (error-prone)

```csharp
// ❌ CURRENT:
while (rdr.Read())
{
    list.Add(new SexualInterestResDTO
    {
        ID = Convert.ToInt32(rdr["ID"]),
        InterestName = Convert.ToString(rdr["InterestName"]),
        InterestDesc = Convert.ToString(rdr["InterestDesc"])
    });
}

// ✅ BETTER (Use Dapper):
Install-Package Dapper

var list = connection.Query<SexualInterestResDTO>(
    "API_V1_spSexualInterest",
    commandType: CommandType.StoredProcedure
).ToList();
```

**Benefits of Dapper:**
- Auto-mapping
- Less code
- Fewer errors
- Better performance

---

### 9. No Unit Tests
**Current:** Test projects are empty

**Action Required:**
```bash
# Install testing packages:
Install-Package xUnit
Install-Package Moq
Install-Package FluentAssertions

# Write tests for AuthService:
[Fact]
public void SendOtp_WithValidMobileNo_ReturnsSuccess()
{
    // Arrange
    var mockDb = new Mock<IMySqlUtilityDb>();
    var service = new AuthService(mockDb.Object);

    // Act
    var result = service.sendOtp("9876543210");

    // Assert
    result.isSuccess.Should().BeTrue();
}
```

**Target:** Write 10 tests this week

---

### 10. No Authentication on Controllers
**File:** Controllers (AuthController.cs, ProfileController.cs)

```csharp
// ✅ ADD [Authorize] attribute:
[Authorize]
[RoutePrefix("api/profile")]
public class ProfileController : ApiController
{
    [HttpGet]
    [Route("")]
    public IHttpActionResult GetProfile()
    {
        var userId = User.Identity.GetUserId();
        // ...
    }

    [AllowAnonymous]  // Explicitly allow public endpoints
    [HttpGet]
    [Route("interests")]
    public IHttpActionResult GetInterests()
    {
        // ...
    }
}
```

---

## ⚪ MEDIUM PRIORITY (Fix Next Sprint)

### 11. Long Parameter List
**File:** `ProfileService.cs`

```csharp
// ❌ CURRENT (10 parameters!):
public vmResponseMessage UpdateUserProfileByID(
    int UserId, string FullName, string Gender, string DOB,
    string Bio, string HeightCm, string City, string LookingFor,
    string Latitude, string Longitude, string[] photos)

// ✅ BETTER (Use DTO):
public vmResponseMessage UpdateUserProfileByID(UpdateProfileRequest request)
{
    // All parameters in one object
}
```

---

### 12. Hardcoded Values
**File:** `UnityConfig.cs`

```csharp
// ❌ CURRENT:
container.RegisterType<IMySqlUtilityDb, MySqlUtilityDb>(
    new ContainerControlledLifetimeManager(),
    new InjectionConstructor("mycon", "u438054979_initiate"));  // Hardcoded

// ✅ BETTER (Use config):
var connectionName = ConfigurationManager.AppSettings["ConnectionName"];
var schema = ConfigurationManager.AppSettings["DbSchema"];
```

---

## ✅ WHAT YOU DID WELL

1. **Architecture:** Clean 3-layer separation (API → BAL → DAL)
2. **DI Container:** Well-configured Unity
3. **AutoMapper:** Good use for DTO mapping
4. **Interfaces:** Repository pattern with interfaces
5. **Thread Safety:** Lock mechanism in MySqlUtilityDb
6. **DataSet Usage:** Good for multiple result sets
7. **Parameterized Queries:** All stored procedures use parameters (prevents SQL injection)

---

## QUESTIONS FOR YOU

### 1. Stored Procedures vs Entity Framework
Why did you choose stored procedures instead of Entity Framework?
- Performance concerns?
- Team preference?
- Project requirement?

**Note:** Stored procedures are fine, but you'll need to:
- Write many more procedures (50+)
- Handle all CRUD operations
- Write comprehensive tests

### 2. Duplicate Projects
Which API project is the active one?
- `initiate.API` or
- `initiateProject`

Can we delete the unused one?

---

## THIS WEEK'S PRIORITIES

### Day 1 (Today)
- [ ] Fix OTP exposure (5 minutes)
- [ ] Answer framework choice question
- [ ] Remove duplicate project
- [ ] Add input validation to sendOtp and verifyOtp

### Day 2
- [ ] Setup Serilog logging
- [ ] Add exception logging to all catch blocks
- [ ] Add [Authorize] attributes to controllers
- [ ] Setup rate limiting for OTP endpoint

### Day 3
- [ ] Remove global lock from MySqlUtilityDb
- [ ] Configure connection pooling
- [ ] Add caching for SexualInterestList
- [ ] Install and configure Redis (optional)

### Day 4-5
- [ ] Write 10 unit tests
- [ ] Setup test database
- [ ] Install Dapper for better mapping
- [ ] Add API documentation (Swagger comments)

---

## RECOMMENDED NUGET PACKAGES

```bash
# Logging
Install-Package Serilog.AspNetCore
Install-Package Serilog.Sinks.File

# Data Access
Install-Package Dapper

# Testing
Install-Package xUnit
Install-Package Moq
Install-Package FluentAssertions

# Validation
Install-Package FluentValidation
Install-Package FluentValidation.AspNetCore

# Caching (if Redis)
Install-Package StackExchange.Redis

# Rate Limiting
Install-Package AspNetCoreRateLimit
```

---

## EXAMPLE: Fixed AuthService

```csharp
public class AuthService : BaseService, IAuthRepo
{
    private readonly ILogger<AuthService> _logger;
    private readonly IRateLimiter _rateLimiter;

    public AuthService(
        IMySqlUtilityDb db,
        ILogger<AuthService> logger,
        IRateLimiter rateLimiter) : base(db)
    {
        _logger = logger;
        _rateLimiter = rateLimiter;
    }

    public vm_userLogin sendOtp(string MobileNo)
    {
        // ✅ Validation
        if (string.IsNullOrWhiteSpace(MobileNo))
        {
            _logger.LogWarning("sendOtp called with empty mobile number");
            return new vm_userLogin
            {
                isSuccess = false,
                ResponseMessage = "Mobile number is required"
            };
        }

        if (!Regex.IsMatch(MobileNo, @"^[6-9]\d{9}$"))
        {
            _logger.LogWarning("sendOtp called with invalid mobile: {MobileNo}", MobileNo);
            return new vm_userLogin
            {
                isSuccess = false,
                ResponseMessage = "Invalid mobile number format"
            };
        }

        // ✅ Rate limiting
        if (!_rateLimiter.AllowRequest($"otp:{MobileNo}", maxRequests: 3, windowMinutes: 60))
        {
            _logger.LogWarning("Rate limit exceeded for mobile: {MobileNo}", MobileNo);
            return new vm_userLogin
            {
                isSuccess = false,
                ResponseMessage = "Too many OTP requests. Please try after 1 hour"
            };
        }

        vm_userLogin response = new vm_userLogin();

        try
        {
            MySqlParameter[] parameters = {
                new MySqlParameter("@MobileNo", MobileNo)
            };

            var reader = _db.fn_DataReader("API_V1_spSendOtp", parameters);

            if (reader.Read())
            {
                response.isSuccess = Convert.ToBoolean(reader["IsSuccess"]);
                // ✅ OTP NOT returned in response
                response.ResponseMessage = reader["ResponseMessage"].ToString();
                response.isProfileCompleted = Convert.ToBoolean(reader["IsProfileCompleted"]);

                _logger.LogInformation("OTP sent successfully to {MobileNo}", MobileNo);
            }
        }
        catch (Exception ex)
        {
            // ✅ Proper logging
            _logger.LogError(ex, "Error sending OTP to {MobileNo}", MobileNo);

            response.isSuccess = false;
            response.ResponseMessage = "An error occurred while sending OTP. Please try again";
        }

        return response;
    }
}
```

---

## RESOURCES

### Logging Setup
```csharp
// Add to Global.asax.cs Application_Start:
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .WriteTo.File("logs/app-.txt", rollingInterval: RollingInterval.Day)
    .WriteTo.Console()
    .CreateLogger();
```

### Rate Limiting Implementation
```csharp
public interface IRateLimiter
{
    bool AllowRequest(string key, int maxRequests, int windowMinutes);
}

public class MemoryRateLimiter : IRateLimiter
{
    private static Dictionary<string, Queue<DateTime>> _requests = new Dictionary<string, Queue<DateTime>>();
    private static object _lock = new object();

    public bool AllowRequest(string key, int maxRequests, int windowMinutes)
    {
        lock (_lock)
        {
            if (!_requests.ContainsKey(key))
                _requests[key] = new Queue<DateTime>();

            var queue = _requests[key];
            var cutoff = DateTime.Now.AddMinutes(-windowMinutes);

            // Remove old requests
            while (queue.Count > 0 && queue.Peek() < cutoff)
                queue.Dequeue();

            if (queue.Count >= maxRequests)
                return false;

            queue.Enqueue(DateTime.Now);
            return true;
        }
    }
}
```

---

## NEXT REVIEW

**Date:** January 24, 2026 (7 days)

**Expected Fixes:**
- ✅ All 🔴 Critical issues resolved
- ✅ Logging implemented
- ✅ Input validation added
- ✅ 10+ unit tests written
- ✅ Framework decision made

---

## NEED HELP?

If you have questions or need clarification on any of these points, please reach out. We're here to help you succeed!

**Good luck, and great job on the architecture!** 👍

---

**END OF DEVELOPER CODE REVIEW**
