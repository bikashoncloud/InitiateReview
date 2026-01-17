# Initiate Dating App - Developer Quick Reference Card
## Print This and Keep at Your Desk!

---

## 🔴 CRITICAL: DO THIS FIRST (Today!)

```bash
# 1. Move secrets to User Secrets
cd InitiateAPI
dotnet user-secrets init
dotnet user-secrets set "ConnectionStrings:cs" "YOUR_DB_CONNECTION"
dotnet user-secrets set "Jwt:Key" "YOUR_JWT_KEY"
dotnet user-secrets set "Jwt:Issuer" "InitiateAppAPI"
dotnet user-secrets set "Jwt:Audience" "InitiateAppUsers"

# 2. Remove OTP from AuthRepository.cs line 41
# Change: response.Data = new { OTP = ... }
# To: response.Data = null;

# 3. Update CORS in Program.cs
# Change: .AllowAnyOrigin()
# To: .WithOrigins("http://localhost:3000")
```

---

## 📋 Daily Workflow Checklist

**Every Morning:**
```
☐ Pull latest code: git pull
☐ Check todos in CODE_REVIEW.md
☐ Review today's tasks from Week 1 plan
☐ Start work on highest priority task
```

**Before Every Commit:**
```
☐ Build succeeds: dotnet build
☐ Tests pass: dotnet test
☐ No compiler warnings
☐ Code formatted properly
☐ Secrets not committed (check git status)
```

**Every Evening:**
```
☐ Commit working code: git add . && git commit -m "message"
☐ Push to GitHub: git push
☐ Update progress in CODE_REVIEW.md
☐ Note blockers for tomorrow
```

---

## 🏗️ Project Structure (Clean Architecture)

```
InitiateAPI/
├── Initiate.Domain/           # Entities, Enums, Interfaces
│   ├── Entities/
│   │   ├── User.cs
│   │   ├── Profile.cs
│   │   ├── Photo.cs
│   │   ├── Like.cs
│   │   ├── Match.cs
│   │   └── Message.cs
│   ├── Enums/
│   └── Interfaces/
│
├── Initiate.Application/      # Business Logic, DTOs, Services
│   ├── Services/
│   │   ├── AuthService.cs
│   │   ├── ProfileService.cs
│   │   └── PhotoService.cs
│   ├── DTOs/
│   ├── Interfaces/
│   └── Validators/
│
├── Initiate.Infrastructure/   # Data Access, External Services
│   ├── Data/
│   │   └── ApplicationDbContext.cs
│   ├── Repositories/
│   ├── Services/
│   └── Caching/
│
└── InitiateAPI/               # Presentation (Controllers)
    ├── Controllers/
    ├── Middleware/
    └── Program.cs
```

---

## 🔑 Essential Commands

### Development
```bash
# Run API
dotnet run --project InitiateAPI

# Build
dotnet build

# Clean
dotnet clean

# Watch (auto-reload)
dotnet watch run --project InitiateAPI
```

### Database (Entity Framework)
```bash
# Add migration
dotnet ef migrations add MigrationName --startup-project InitiateAPI

# Update database
dotnet ef database update --startup-project InitiateAPI

# Remove last migration
dotnet ef migrations remove

# Drop database
dotnet ef database drop
```

### Testing
```bash
# Run all tests
dotnet test

# Run with details
dotnet test --logger "console;verbosity=detailed"

# Run specific test
dotnet test --filter "FullyQualifiedName~AuthServiceTests"

# Generate coverage
dotnet test --collect:"XPlat Code Coverage"
```

### Packages
```bash
# Add package
dotnet add package PackageName

# Add specific version
dotnet add package PackageName --version 8.0.0

# Remove package
dotnet remove package PackageName

# List packages
dotnet list package
```

### User Secrets
```bash
# List all secrets
dotnet user-secrets list

# Set secret
dotnet user-secrets set "Key" "Value"

# Remove secret
dotnet user-secrets remove "Key"

# Clear all
dotnet user-secrets clear
```

### Git
```bash
# Status
git status

# Stage all changes
git add .

# Commit
git commit -m "feat: add photo upload"

# Push
git push

# Pull
git pull

# Create branch
git checkout -b feature/photo-upload

# Switch branch
git checkout main
```

---

## 📦 Required NuGet Packages

### InitiateAPI Project
```
Microsoft.AspNetCore.Authentication.JwtBearer (8.0.*)
Serilog.AspNetCore
Serilog.Sinks.File
Serilog.Sinks.Console
```

### Initiate.Application Project
```
FluentValidation (11.*)
FluentValidation.AspNetCore
AutoMapper
```

### Initiate.Infrastructure Project
```
Pomelo.EntityFrameworkCore.MySql (8.0.*)
Microsoft.EntityFrameworkCore.Design
StackExchange.Redis
AWSSDK.S3
SixLabors.ImageSharp
```

### Initiate.UnitTests Project
```
xunit
xunit.runner.visualstudio
Moq
FluentAssertions
Microsoft.NET.Test.Sdk
```

---

## 🎯 Week 1 Tasks (Jan 6-10)

### Monday: Security Fixes ⚡
```
1. Move secrets to User Secrets ✓
2. Remove OTP from response ✓
3. Fix CORS policy ✓
4. Add input validation ✓
5. Test all endpoints ✓
```

### Tuesday: Clean Architecture 🏗️
```
1. Create 3 new projects ✓
2. Add project references ✓
3. Create domain entities ✓
4. Create DTOs ✓
5. Create service interfaces ✓
```

### Wednesday: Photo Upload 📸
```
1. Create PhotoController ✓
2. Implement upload endpoint ✓
3. Add file validation ✓
4. Implement delete/reorder ✓
5. Test with Postman ✓
```

### Thursday: Logging & Validation 📝
```
1. Setup Serilog ✓
2. Add FluentValidation ✓
3. Create exception middleware ✓
4. Test error handling ✓
5. Review logs ✓
```

### Friday: Testing & Review ✅
```
1. Create test project ✓
2. Write unit tests ✓
3. Code cleanup ✓
4. Self-review ✓
5. Prepare demo ✓
```

---

## 🐛 Common Errors & Quick Fixes

### "User Secrets not found"
```bash
dotnet user-secrets init
```

### "Cannot connect to MySQL"
```bash
# Check MySQL is running
sudo systemctl status mysql

# Or Windows:
# services.msc -> MySQL80 -> Start
```

### "Migration failed"
```bash
# Drop database and recreate
dotnet ef database drop --force
dotnet ef database update
```

### "Redis connection failed"
```bash
# Start Redis Docker container
docker run -d -p 6379:6379 redis:latest

# Or check if Redis is running
redis-cli ping
```

### "Assembly reference error"
```bash
# Clean and rebuild
dotnet clean
dotnet build
```

### "Port already in use"
```bash
# Kill process on port 5000
# Windows:
netstat -ano | findstr :5000
taskkill /PID <PID> /F

# Linux:
sudo lsof -i :5000
sudo kill -9 <PID>
```

---

## 🧪 Postman Quick Tests

### 1. Send OTP
```http
POST http://localhost:5000/api/auth/sendotp
Content-Type: application/json

{"mobileNo": "9876543210"}
```
**Expected:** 200 OK, message "OTP sent successfully"

### 2. Verify OTP
```http
POST http://localhost:5000/api/auth/verifyotp
Content-Type: application/json

{"mobileNo": "9876543210", "otp": "1234"}
```
**Expected:** 200 OK, JWT token returned

### 3. Get Profile (Protected)
```http
GET http://localhost:5000/api/profile
Authorization: Bearer <YOUR_TOKEN>
```
**Expected:** 200 OK, profile data

### 4. Upload Photo
```http
POST http://localhost:5000/api/photos/upload
Authorization: Bearer <YOUR_TOKEN>
Content-Type: multipart/form-data

file: [SELECT FILE]
```
**Expected:** 200 OK, photo URL

---

## 📊 Code Quality Checklist

**Before Every Commit:**
```
☐ No hardcoded credentials
☐ No console.WriteLine (use ILogger)
☐ All async methods use await
☐ No unused using statements
☐ Proper error handling (try-catch)
☐ Input validation present
☐ XML comments on public methods
☐ Tests for new code
☐ Follows naming conventions
```

**Naming Conventions:**
```csharp
// Classes: PascalCase
public class UserService { }

// Methods: PascalCase
public async Task<User> GetUserAsync() { }

// Private fields: _camelCase
private readonly IUserRepository _repository;

// Parameters: camelCase
public void SaveUser(long userId, string userName) { }

// Constants: UPPER_CASE
private const int MAX_FILE_SIZE = 10485760;

// Async methods: suffix with Async
public async Task<bool> SendEmailAsync() { }
```

---

## 🔒 Security Checklist

**NEVER Commit:**
```
✗ Database passwords
✗ JWT secret keys
✗ API keys (AWS, Twilio, etc.)
✗ appsettings.Production.json with secrets
✗ .env files with secrets
```

**ALWAYS:**
```
✓ Use User Secrets (development)
✓ Use Azure Key Vault (production)
✓ Validate all user input
✓ Use parameterized queries
✓ Sanitize error messages (production)
✓ Use HTTPS in production
✓ Implement rate limiting
✓ Log security events
```

---

## 🚀 Performance Tips

**Do:**
```csharp
// ✅ Use async/await
await _repository.GetUserAsync(id);

// ✅ Cache frequently accessed data
var cached = await _cache.GetAsync<User>($"user:{id}");

// ✅ Use pagination
var users = await _repository.GetUsersAsync(page: 1, pageSize: 20);

// ✅ Select only needed columns
.Select(u => new { u.Id, u.Name })

// ✅ Use indexes
[Index(nameof(UserId), nameof(CreatedAt))]
```

**Don't:**
```csharp
// ❌ Block async calls
var user = _repository.GetUserAsync(id).Result;

// ❌ N+1 queries
foreach(var user in users) {
    var profile = await _repo.GetProfileAsync(user.Id);
}

// ❌ Load all data
var allUsers = await _repository.GetAllUsersAsync();
```

---

## 📞 Emergency Contacts

**Blocked on:**
- Security issues → Review CODE_REVIEW.md Section 2
- Architecture → Review DEVELOPER_IMPLEMENTATION_GUIDE.md Section 3
- Testing → Review DEVELOPER_IMPLEMENTATION_GUIDE.md Section 10

**Resources:**
- Project Manager: Bikash Kumar
- Documentation: `/InitiateReview/docs/`
- Code Review: `CODE_REVIEW.md`
- Implementation Guide: `DEVELOPER_IMPLEMENTATION_GUIDE.md`

---

## ⏰ Time Management

**Recommended Daily Schedule:**
```
09:00 - 09:15  Daily standup
09:15 - 11:00  Focus work (no interruptions)
11:00 - 11:15  Break
11:15 - 13:00  Focus work
13:00 - 14:00  Lunch
14:00 - 16:00  Focus work
16:00 - 16:15  Break
16:15 - 17:30  Testing & documentation
17:30 - 18:00  Code review & commit
```

**Pomodoro Technique:**
- 25 minutes focused work
- 5 minutes break
- Repeat 4 times
- Take 15-30 minute break

---

## 🎯 Definition of Done

**A task is DONE when:**
```
✅ Code written and works locally
✅ Unit tests written (>50% coverage)
✅ Code reviewed (self or peer)
✅ No compiler warnings
✅ Documented (XML comments)
✅ Committed to Git
✅ Tested in Postman
✅ Checked in CODE_REVIEW.md
```

---

## 💡 Pro Tips

1. **Commit often** - Small commits are easier to review and revert
2. **Write tests first** - TDD prevents bugs
3. **Use breakpoints** - Debug efficiently with VS debugger
4. **Read logs** - Check `logs/` folder when things break
5. **Ask early** - Don't spend >30 min stuck on same issue
6. **Comment why, not what** - Code shows what, comments explain why
7. **Keep it simple** - KISS principle always wins
8. **Refactor later** - Get it working first, then optimize

---

## 📈 Success Metrics

**Track daily:**
- [ ] Tasks completed from Week 1 plan
- [ ] Test coverage % (target: >50%)
- [ ] Lines of code added
- [ ] Bugs fixed
- [ ] Features completed

**Week 1 Goal:**
- Security issues: 0 critical
- Test coverage: >50%
- Features: Auth + Profile + Photos working
- Architecture: Clean Architecture implemented

---

## 🎉 Motivational Reminders

**When stuck:**
> "It always seems impossible until it's done" - Nelson Mandela

**When tired:**
> "Rest when you're weary, but don't quit"

**When overwhelmed:**
> "Focus on progress, not perfection. MVP over perfect code."

**Always remember:**
> "Launch Date: February 20, 2026 - We got this! 🚀"

---

**Print this page and keep it visible at your desk!**

**Good luck with Week 1! 💪**

---

**Version:** 1.0
**Date:** January 5, 2026
**Next Review:** Saturday, January 10, 2026
