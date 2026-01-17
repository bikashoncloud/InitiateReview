# Initiate Dating App - Developer Implementation Guide
## Complete Step-by-Step Reference for .NET API Development

**Version:** 1.0
**Date:** January 5, 2026
**Target Audience:** .NET Backend Developers
**Prerequisite:** Read CODE_REVIEW.md first

---

## 📚 Table of Contents

1. [Quick Start - Development Environment Setup](#1-quick-start---development-environment-setup)
2. [CRITICAL: Security Fixes (Do This First!)](#2-critical-security-fixes-do-this-first)
3. [Clean Architecture Implementation](#3-clean-architecture-implementation)
4. [Photo Upload Implementation](#4-photo-upload-implementation)
5. [MySQL Migration Guide](#5-mysql-migration-guide)
6. [Redis Caching Implementation](#6-redis-caching-implementation)
7. [Logging Setup (Serilog)](#7-logging-setup-serilog)
8. [Input Validation (FluentValidation)](#8-input-validation-fluentvalidation)
9. [Global Exception Handling](#9-global-exception-handling)
10. [Unit Testing Setup](#10-unit-testing-setup)
11. [Complete Code Templates](#11-complete-code-templates)
12. [Daily Development Checklist](#12-daily-development-checklist)

---

## 1. Quick Start - Development Environment Setup

### Prerequisites Installation

**Required Tools:**
```bash
# 1. Install .NET 8 SDK
winget install Microsoft.DotNet.SDK.8

# 2. Install Visual Studio 2022 or VS Code
winget install Microsoft.VisualStudio.2022.Community

# 3. Install MySQL Server 8.0+
winget install Oracle.MySQL

# 4. Install Redis (Windows)
# Download from: https://github.com/microsoftarchive/redis/releases
# Or use Docker: docker run -d -p 6379:6379 redis:latest

# 5. Install Git
winget install Git.Git

# 6. Install Postman (API Testing)
winget install Postman.Postman
```

**Required VS Code Extensions:**
- C# Dev Kit
- NuGet Package Manager
- REST Client
- GitLens

---

## 2. CRITICAL: Security Fixes (Do This First!)

### 🔴 Fix #1: Remove Credentials from appsettings.json

**Step 1: Enable User Secrets**

```bash
# Navigate to your API project directory
cd "C:\Users\bikash.b.kumar\OneDrive - Accenture\Documents\MyDocs\App Designs\Repos\initiate\InitiateAPI\InitiateAPI"

# Initialize user secrets
dotnet user-secrets init
```

**Step 2: Add secrets to User Secrets (Development)**

```bash
# Add connection string
dotnet user-secrets set "ConnectionStrings:cs" "server=dbp.dotplus.app,53125;database=admin_ashutosh;user id=admin_ashutosh;password=J?A5xs5sNk@qwx1a;TrustServerCertificate=True;"

# Add JWT configuration
dotnet user-secrets set "Jwt:Key" "$#InitateAPI!@#$%^&JWT&*()_+Token2025"
dotnet user-secrets set "Jwt:Issuer" "InitiateAppAPI"
dotnet user-secrets set "Jwt:Audience" "InitiateAppUsers"
```

**Step 3: Update appsettings.json (Remove Sensitive Data)**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "cs": "TO_BE_REPLACED_BY_USER_SECRETS_OR_ENV_VARS"
  },
  "AllowedHosts": "*",
  "Jwt": {
    "Key": "TO_BE_REPLACED_BY_USER_SECRETS_OR_ENV_VARS",
    "Issuer": "TO_BE_REPLACED_BY_USER_SECRETS_OR_ENV_VARS",
    "Audience": "TO_BE_REPLACED_BY_USER_SECRETS_OR_ENV_VARS"
  }
}
```

**Step 4: Update appsettings.Production.json (For Production)**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft.AspNetCore": "Error"
    }
  },
  "AllowedHosts": "*"
}
```

**Step 5: Update .gitignore**

```bash
# Add these lines to .gitignore
*.user
appsettings.Development.json
appsettings.Production.json
secrets.json
```

---

### 🔴 Fix #2: Remove OTP from API Response

**File:** `Repository/AuthRepository.cs`

**BEFORE (Line 36-43):**
```csharp
if (response.IsSuccess == true)
{
    response.StatusCode = 200;
    response.Data = new
    {
        OTP = reader["Otp"].ToString()  // ❌ REMOVE THIS
    };
}
```

**AFTER:**
```csharp
if (response.IsSuccess == true)
{
    response.StatusCode = 200;
    response.Message = "OTP sent successfully to your mobile number";
    response.Data = null;  // ✅ Don't return OTP
}
```

---

### 🔴 Fix #3: Update CORS Policy

**File:** `Program.cs`

**BEFORE (Line 53-62):**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll", policy =>
    {
        policy
            .AllowAnyOrigin()   // ❌ SECURITY RISK
            .AllowAnyMethod()
            .AllowAnyHeader();
    });
});
```

**AFTER:**
```csharp
builder.Services.AddCors(options =>
{
    // Development CORS policy
    options.AddPolicy("Development", policy =>
    {
        policy
            .WithOrigins("http://localhost:3000", "http://localhost:4200")
            .AllowAnyMethod()
            .AllowAnyHeader()
            .AllowCredentials();
    });

    // Production CORS policy
    options.AddPolicy("Production", policy =>
    {
        policy
            .WithOrigins("https://yourdomain.com", "https://www.yourdomain.com")
            .AllowAnyMethod()
            .AllowAnyHeader()
            .AllowCredentials();
    });
});
```

**Update middleware (Line 75):**
```csharp
// Use environment-specific CORS
app.UseCors(app.Environment.IsDevelopment() ? "Development" : "Production");
```

---

### 🔴 Fix #4: Add Input Validation to Profile

**File:** `Model/Profile.cs`

**BEFORE:**
```csharp
public class ProfileRequest
{
    public string? FullName { get; set; }
    public string? Gender { get; set; }
    public DateTime DOB { get; set; }
    public string? Bio { get; set; }
    public int HeightCm { get; set; }
    public string? City { get; set; }
    public string? LookingFor { get; set; }
}
```

**AFTER:**
```csharp
using System.ComponentModel.DataAnnotations;

public class ProfileRequest
{
    [Required(ErrorMessage = "Full name is required")]
    [StringLength(100, MinimumLength = 2, ErrorMessage = "Name must be between 2 and 100 characters")]
    public string FullName { get; set; }

    [Required(ErrorMessage = "Gender is required")]
    [RegularExpression("^(Male|Female|Other)$", ErrorMessage = "Gender must be Male, Female, or Other")]
    public string Gender { get; set; }

    [Required(ErrorMessage = "Date of birth is required")]
    [DataType(DataType.Date)]
    public DateTime DOB { get; set; }

    [StringLength(500, ErrorMessage = "Bio cannot exceed 500 characters")]
    public string? Bio { get; set; }

    [Required(ErrorMessage = "Height is required")]
    [Range(120, 250, ErrorMessage = "Height must be between 120cm and 250cm")]
    public int HeightCm { get; set; }

    [Required(ErrorMessage = "City is required")]
    [StringLength(100, MinimumLength = 2, ErrorMessage = "City must be between 2 and 100 characters")]
    public string City { get; set; }

    [Required(ErrorMessage = "Looking for preference is required")]
    public string LookingFor { get; set; }
}
```

**Add Age Validation Attribute:**

Create new file: `Helper/AgeValidationAttribute.cs`

```csharp
using System.ComponentModel.DataAnnotations;

namespace InitiateAPI.Helper
{
    public class AgeValidationAttribute : ValidationAttribute
    {
        private readonly int _minAge;
        private readonly int _maxAge;

        public AgeValidationAttribute(int minAge = 18, int maxAge = 100)
        {
            _minAge = minAge;
            _maxAge = maxAge;
        }

        protected override ValidationResult IsValid(object value, ValidationContext validationContext)
        {
            if (value is DateTime dateOfBirth)
            {
                var age = DateTime.Today.Year - dateOfBirth.Year;
                if (dateOfBirth.Date > DateTime.Today.AddYears(-age)) age--;

                if (age < _minAge)
                    return new ValidationResult($"You must be at least {_minAge} years old");

                if (age > _maxAge)
                    return new ValidationResult($"Age cannot exceed {_maxAge} years");

                return ValidationResult.Success;
            }

            return new ValidationResult("Invalid date format");
        }
    }
}
```

**Update ProfileRequest to use it:**
```csharp
[Required(ErrorMessage = "Date of birth is required")]
[DataType(DataType.Date)]
[AgeValidation(18, 100)]  // ✅ Add age validation
public DateTime DOB { get; set; }
```

---

### 🔴 Fix #5: Add Model Validation Filter

**Create new file:** `Filters/ValidationFilter.cs`

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;
using InitiateAPI.Model;

namespace InitiateAPI.Filters
{
    public class ValidationFilter : IActionFilter
    {
        public void OnActionExecuting(ActionExecutingContext context)
        {
            if (!context.ModelState.IsValid)
            {
                var errors = context.ModelState
                    .Where(x => x.Value.Errors.Count > 0)
                    .Select(x => new
                    {
                        Field = x.Key,
                        Errors = x.Value.Errors.Select(e => e.ErrorMessage).ToArray()
                    })
                    .ToList();

                var response = new Response
                {
                    IsSuccess = false,
                    StatusCode = 400,
                    Message = "Validation failed",
                    Data = errors
                };

                context.Result = new BadRequestObjectResult(response);
            }
        }

        public void OnActionExecuted(ActionExecutedContext context) { }
    }
}
```

**Register in Program.cs:**
```csharp
builder.Services.AddControllers(options =>
{
    options.Filters.Add<ValidationFilter>();
});
```

---

## 3. Clean Architecture Implementation

### Step 1: Create Project Structure

**Open terminal in solution directory:**

```bash
cd "C:\Users\bikash.b.kumar\OneDrive - Accenture\Documents\MyDocs\App Designs\Repos\initiate\InitiateAPI"

# Create Domain project
dotnet new classlib -n Initiate.Domain
dotnet sln add Initiate.Domain/Initiate.Domain.csproj

# Create Application project
dotnet new classlib -n Initiate.Application
dotnet sln add Initiate.Application/Initiate.Application.csproj

# Create Infrastructure project
dotnet new classlib -n Initiate.Infrastructure
dotnet sln add Initiate.Infrastructure/Initiate.Infrastructure.csproj

# Add project references
cd Initiate.Application
dotnet add reference ../Initiate.Domain/Initiate.Domain.csproj

cd ../Initiate.Infrastructure
dotnet add reference ../Initiate.Domain/Initiate.Domain.csproj
dotnet add reference ../Initiate.Application/Initiate.Application.csproj

cd ../InitiateAPI
dotnet add reference ../Initiate.Application/Initiate.Application.csproj
dotnet add reference ../Initiate.Infrastructure/Initiate.Infrastructure.csproj
```

**Final Structure:**
```
InitiateAPI/
├── InitiateAPI/           # Presentation Layer (Controllers, Middleware)
├── Initiate.Application/  # Business Logic (Services, DTOs, Validators)
├── Initiate.Domain/       # Core Domain (Entities, Enums, Interfaces)
└── Initiate.Infrastructure/ # Data Access (Repositories, External Services)
```

---

### Step 2: Create Domain Entities

**File:** `Initiate.Domain/Entities/User.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class User
    {
        public long Id { get; set; }
        public string MobileNo { get; set; }
        public string? Email { get; set; }
        public DateTime CreatedAt { get; set; }
        public DateTime? LastLoginAt { get; set; }
        public bool IsActive { get; set; }
        public bool IsDeleted { get; set; }

        // Navigation properties
        public Profile Profile { get; set; }
        public ICollection<Photo> Photos { get; set; }
        public ICollection<Like> SentLikes { get; set; }
        public ICollection<Like> ReceivedLikes { get; set; }
        public ICollection<Match> Matches { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Profile.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Profile
    {
        public long Id { get; set; }
        public long UserId { get; set; }
        public string FullName { get; set; }
        public string Gender { get; set; }
        public DateTime DateOfBirth { get; set; }
        public string? Bio { get; set; }
        public int HeightCm { get; set; }
        public string City { get; set; }
        public double? Latitude { get; set; }
        public double? Longitude { get; set; }
        public string? Occupation { get; set; }
        public string? Education { get; set; }
        public string LookingFor { get; set; }
        public int? AgeRangeMin { get; set; }
        public int? AgeRangeMax { get; set; }
        public int? MaxDistanceKm { get; set; }
        public bool IsVerified { get; set; }
        public bool IsProfileCompleted { get; set; }
        public DateTime CreatedAt { get; set; }
        public DateTime? UpdatedAt { get; set; }

        // Navigation
        public User User { get; set; }
        public ICollection<Interest> Interests { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Photo.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Photo
    {
        public long Id { get; set; }
        public long UserId { get; set; }
        public string PhotoUrl { get; set; }
        public string? ThumbnailUrl { get; set; }
        public int DisplayOrder { get; set; }
        public bool IsPrimary { get; set; }
        public DateTime UploadedAt { get; set; }

        // Navigation
        public User User { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Like.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Like
    {
        public long Id { get; set; }
        public long FromUserId { get; set; }
        public long ToUserId { get; set; }
        public bool IsLike { get; set; }  // true = like, false = dislike
        public DateTime CreatedAt { get; set; }

        // Navigation
        public User FromUser { get; set; }
        public User ToUser { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Match.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Match
    {
        public long Id { get; set; }
        public long User1Id { get; set; }
        public long User2Id { get; set; }
        public DateTime MatchedAt { get; set; }
        public bool IsActive { get; set; }

        // Navigation
        public User User1 { get; set; }
        public User User2 { get; set; }
        public ICollection<Message> Messages { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Message.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Message
    {
        public long Id { get; set; }
        public long MatchId { get; set; }
        public long SenderId { get; set; }
        public long ReceiverId { get; set; }
        public string Content { get; set; }
        public DateTime SentAt { get; set; }
        public DateTime? ReadAt { get; set; }
        public bool IsDeleted { get; set; }

        // Navigation
        public Match Match { get; set; }
    }
}
```

**File:** `Initiate.Domain/Entities/Interest.cs`

```csharp
namespace Initiate.Domain.Entities
{
    public class Interest
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string? Description { get; set; }
        public string? Icon { get; set; }

        // Navigation
        public ICollection<Profile> Profiles { get; set; }
    }
}
```

---

### Step 3: Create Domain Enums

**File:** `Initiate.Domain/Enums/Gender.cs`

```csharp
namespace Initiate.Domain.Enums
{
    public enum Gender
    {
        Male = 1,
        Female = 2,
        Other = 3
    }
}
```

**File:** `Initiate.Domain/Enums/LookingFor.cs`

```csharp
namespace Initiate.Domain.Enums
{
    public enum LookingFor
    {
        Friendship = 1,
        Dating = 2,
        Relationship = 3,
        Marriage = 4,
        Casual = 5
    }
}
```

---

### Step 4: Create Application DTOs

**File:** `Initiate.Application/DTOs/Auth/AuthRequestDto.cs`

```csharp
namespace Initiate.Application.DTOs.Auth
{
    public class SendOtpRequestDto
    {
        public string MobileNo { get; set; }
    }

    public class VerifyOtpRequestDto
    {
        public string MobileNo { get; set; }
        public string Otp { get; set; }
    }
}
```

**File:** `Initiate.Application/DTOs/Auth/AuthResponseDto.cs`

```csharp
namespace Initiate.Application.DTOs.Auth
{
    public class AuthResponseDto
    {
        public string Token { get; set; }
        public bool IsProfileCompleted { get; set; }
        public long UserId { get; set; }
    }
}
```

**File:** `Initiate.Application/DTOs/Profile/ProfileDto.cs`

```csharp
namespace Initiate.Application.DTOs.Profile
{
    public class ProfileDto
    {
        public long UserId { get; set; }
        public string FullName { get; set; }
        public string Gender { get; set; }
        public DateTime DateOfBirth { get; set; }
        public int Age { get; set; }
        public string? Bio { get; set; }
        public int HeightCm { get; set; }
        public string City { get; set; }
        public string LookingFor { get; set; }
        public bool IsVerified { get; set; }
        public List<PhotoDto> Photos { get; set; }
        public List<string> Interests { get; set; }
    }

    public class PhotoDto
    {
        public long Id { get; set; }
        public string PhotoUrl { get; set; }
        public int DisplayOrder { get; set; }
        public bool IsPrimary { get; set; }
    }

    public class UpdateProfileRequestDto
    {
        public string FullName { get; set; }
        public string Gender { get; set; }
        public DateTime DateOfBirth { get; set; }
        public string? Bio { get; set; }
        public int HeightCm { get; set; }
        public string City { get; set; }
        public string LookingFor { get; set; }
        public List<int>? InterestIds { get; set; }
    }
}
```

---

### Step 5: Create Service Interfaces

**File:** `Initiate.Application/Interfaces/IAuthService.cs`

```csharp
using Initiate.Application.DTOs.Auth;

namespace Initiate.Application.Interfaces
{
    public interface IAuthService
    {
        Task<ServiceResult<string>> SendOtpAsync(SendOtpRequestDto request);
        Task<ServiceResult<AuthResponseDto>> VerifyOtpAsync(VerifyOtpRequestDto request);
        Task<ServiceResult<bool>> ValidateTokenAsync(string token);
        Task<ServiceResult<bool>> LogoutAsync(string token);
    }
}
```

**File:** `Initiate.Application/Interfaces/IProfileService.cs`

```csharp
using Initiate.Application.DTOs.Profile;

namespace Initiate.Application.Interfaces
{
    public interface IProfileService
    {
        Task<ServiceResult<ProfileDto>> GetProfileAsync(long userId);
        Task<ServiceResult<ProfileDto>> UpdateProfileAsync(long userId, UpdateProfileRequestDto request);
        Task<ServiceResult<List<InterestDto>>> GetInterestsAsync();
    }
}
```

**File:** `Initiate.Application/Common/ServiceResult.cs`

```csharp
namespace Initiate.Application.Common
{
    public class ServiceResult<T>
    {
        public bool IsSuccess { get; set; }
        public string Message { get; set; }
        public T Data { get; set; }
        public List<string> Errors { get; set; } = new();

        public static ServiceResult<T> Success(T data, string message = "Success")
        {
            return new ServiceResult<T>
            {
                IsSuccess = true,
                Message = message,
                Data = data
            };
        }

        public static ServiceResult<T> Failure(string message, List<string> errors = null)
        {
            return new ServiceResult<T>
            {
                IsSuccess = false,
                Message = message,
                Errors = errors ?? new List<string>()
            };
        }
    }
}
```

---

### Step 6: Implement Services

**File:** `Initiate.Application/Services/AuthService.cs`

```csharp
using Initiate.Application.DTOs.Auth;
using Initiate.Application.Interfaces;
using Initiate.Application.Common;
using Initiate.Domain.Interfaces;

namespace Initiate.Application.Services
{
    public class AuthService : IAuthService
    {
        private readonly IAuthRepository _authRepository;
        private readonly IJwtTokenService _jwtTokenService;
        private readonly IOtpService _otpService;
        private readonly ILogger<AuthService> _logger;

        public AuthService(
            IAuthRepository authRepository,
            IJwtTokenService jwtTokenService,
            IOtpService otpService,
            ILogger<AuthService> logger)
        {
            _authRepository = authRepository;
            _jwtTokenService = jwtTokenService;
            _otpService = otpService;
            _logger = logger;
        }

        public async Task<ServiceResult<string>> SendOtpAsync(SendOtpRequestDto request)
        {
            try
            {
                // Validate mobile number
                if (!IsValidIndianMobileNumber(request.MobileNo))
                {
                    return ServiceResult<string>.Failure("Invalid mobile number format");
                }

                // Generate OTP
                var otp = _otpService.GenerateOtp();

                // Save OTP to database
                var saved = await _authRepository.SaveOtpAsync(request.MobileNo, otp);
                if (!saved)
                {
                    return ServiceResult<string>.Failure("Failed to generate OTP");
                }

                // Send OTP via SMS (implement in infrastructure)
                await _otpService.SendOtpSmsAsync(request.MobileNo, otp);

                _logger.LogInformation("OTP sent to {MobileNo}", request.MobileNo);

                return ServiceResult<string>.Success(null, "OTP sent successfully");
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error sending OTP to {MobileNo}", request.MobileNo);
                return ServiceResult<string>.Failure("Failed to send OTP");
            }
        }

        public async Task<ServiceResult<AuthResponseDto>> VerifyOtpAsync(VerifyOtpRequestDto request)
        {
            try
            {
                // Verify OTP from database
                var user = await _authRepository.VerifyOtpAsync(request.MobileNo, request.Otp);

                if (user == null)
                {
                    return ServiceResult<AuthResponseDto>.Failure("Invalid or expired OTP");
                }

                // Generate JWT token
                var token = _jwtTokenService.GenerateToken(user.Id, user.MobileNo);

                // Save token to database
                await _authRepository.SaveTokenAsync(user.Id, token);

                var response = new AuthResponseDto
                {
                    Token = token,
                    IsProfileCompleted = user.Profile?.IsProfileCompleted ?? false,
                    UserId = user.Id
                };

                _logger.LogInformation("User {UserId} logged in successfully", user.Id);

                return ServiceResult<AuthResponseDto>.Success(response, "Login successful");
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error verifying OTP for {MobileNo}", request.MobileNo);
                return ServiceResult<AuthResponseDto>.Failure("Failed to verify OTP");
            }
        }

        public async Task<ServiceResult<bool>> ValidateTokenAsync(string token)
        {
            try
            {
                var isValid = await _authRepository.ValidateTokenAsync(token);
                return isValid
                    ? ServiceResult<bool>.Success(true, "Token is valid")
                    : ServiceResult<bool>.Failure("Invalid token");
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error validating token");
                return ServiceResult<bool>.Failure("Failed to validate token");
            }
        }

        public async Task<ServiceResult<bool>> LogoutAsync(string token)
        {
            try
            {
                var removed = await _authRepository.RemoveTokenAsync(token);
                return removed
                    ? ServiceResult<bool>.Success(true, "Logged out successfully")
                    : ServiceResult<bool>.Failure("Logout failed");
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error during logout");
                return ServiceResult<bool>.Failure("Failed to logout");
            }
        }

        private bool IsValidIndianMobileNumber(string mobileNo)
        {
            return System.Text.RegularExpressions.Regex.IsMatch(mobileNo, @"^[6-9][0-9]{9}$");
        }
    }
}
```

---

## 4. Photo Upload Implementation

### Step 1: Install Required Packages

```bash
cd InitiateAPI
dotnet add package AWSSDK.S3
dotnet add package SixLabors.ImageSharp
```

### Step 2: Create Photo Service

**File:** `Initiate.Application/Interfaces/IPhotoService.cs`

```csharp
using Microsoft.AspNetCore.Http;

namespace Initiate.Application.Interfaces
{
    public interface IPhotoService
    {
        Task<ServiceResult<PhotoDto>> UploadPhotoAsync(long userId, IFormFile file);
        Task<ServiceResult<bool>> DeletePhotoAsync(long userId, long photoId);
        Task<ServiceResult<bool>> SetPrimaryPhotoAsync(long userId, long photoId);
        Task<ServiceResult<List<PhotoDto>>> GetUserPhotosAsync(long userId);
        Task<ServiceResult<bool>> ReorderPhotosAsync(long userId, List<long> photoIds);
    }
}
```

**File:** `Initiate.Infrastructure/Services/PhotoStorageService.cs`

```csharp
using Amazon.S3;
using Amazon.S3.Model;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Processing;

namespace Initiate.Infrastructure.Services
{
    public class PhotoStorageService : IPhotoStorageService
    {
        private readonly IAmazonS3 _s3Client;
        private readonly IConfiguration _configuration;
        private readonly ILogger<PhotoStorageService> _logger;
        private readonly string _bucketName;

        public PhotoStorageService(
            IAmazonS3 s3Client,
            IConfiguration configuration,
            ILogger<PhotoStorageService> logger)
        {
            _s3Client = s3Client;
            _configuration = configuration;
            _logger = logger;
            _bucketName = configuration["AWS:S3BucketName"];
        }

        public async Task<(string photoUrl, string thumbnailUrl)> UploadPhotoAsync(
            Stream fileStream,
            string fileName,
            long userId)
        {
            try
            {
                // Generate unique file names
                var uniqueFileName = $"users/{userId}/photos/{Guid.NewGuid()}_{fileName}";
                var thumbnailFileName = $"users/{userId}/thumbnails/{Guid.NewGuid()}_{fileName}";

                // Upload original photo
                var photoUrl = await UploadToS3Async(fileStream, uniqueFileName, "image/jpeg");

                // Create and upload thumbnail
                fileStream.Position = 0;
                using var image = await Image.LoadAsync(fileStream);
                image.Mutate(x => x.Resize(new ResizeOptions
                {
                    Size = new Size(300, 300),
                    Mode = ResizeMode.Crop
                }));

                using var thumbnailStream = new MemoryStream();
                await image.SaveAsJpegAsync(thumbnailStream);
                thumbnailStream.Position = 0;

                var thumbnailUrl = await UploadToS3Async(thumbnailStream, thumbnailFileName, "image/jpeg");

                return (photoUrl, thumbnailUrl);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error uploading photo for user {UserId}", userId);
                throw;
            }
        }

        public async Task<bool> DeletePhotoAsync(string photoUrl)
        {
            try
            {
                var key = GetKeyFromUrl(photoUrl);
                var deleteRequest = new DeleteObjectRequest
                {
                    BucketName = _bucketName,
                    Key = key
                };

                await _s3Client.DeleteObjectAsync(deleteRequest);
                return true;
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error deleting photo {PhotoUrl}", photoUrl);
                return false;
            }
        }

        private async Task<string> UploadToS3Async(Stream stream, string key, string contentType)
        {
            var putRequest = new PutObjectRequest
            {
                BucketName = _bucketName,
                Key = key,
                InputStream = stream,
                ContentType = contentType,
                CannedACL = S3CannedACL.PublicRead
            };

            await _s3Client.PutObjectAsync(putRequest);

            return $"https://{_bucketName}.s3.amazonaws.com/{key}";
        }

        private string GetKeyFromUrl(string url)
        {
            var uri = new Uri(url);
            return uri.AbsolutePath.TrimStart('/');
        }
    }
}
```

### Step 3: Create Photo Controller

**File:** `InitiateAPI/Controllers/PhotoController.cs`

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Initiate.Application.Interfaces;
using InitiateAPI.Helper;

namespace InitiateAPI.Controllers
{
    [Route("api/photos")]
    [ApiController]
    [Authorize]
    public class PhotoController : ControllerBase
    {
        private readonly IPhotoService _photoService;
        private readonly ILogger<PhotoController> _logger;

        public PhotoController(IPhotoService photoService, ILogger<PhotoController> logger)
        {
            _photoService = photoService;
            _logger = logger;
        }

        [HttpPost("upload")]
        public async Task<IActionResult> UploadPhoto([FromForm] IFormFile file)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);

                // Validate file
                if (file == null || file.Length == 0)
                {
                    return BadRequest(new { message = "No file uploaded" });
                }

                // Validate file size (max 10MB)
                if (file.Length > 10 * 1024 * 1024)
                {
                    return BadRequest(new { message = "File size exceeds 10MB limit" });
                }

                // Validate file type
                var allowedTypes = new[] { "image/jpeg", "image/jpg", "image/png" };
                if (!allowedTypes.Contains(file.ContentType.ToLower()))
                {
                    return BadRequest(new { message = "Only JPEG and PNG images are allowed" });
                }

                var result = await _photoService.UploadPhotoAsync(userId, file);

                if (result.IsSuccess)
                {
                    return Ok(result);
                }

                return BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error uploading photo");
                return StatusCode(500, new { message = "Failed to upload photo" });
            }
        }

        [HttpGet]
        public async Task<IActionResult> GetPhotos()
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _photoService.GetUserPhotosAsync(userId);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error fetching photos");
                return StatusCode(500, new { message = "Failed to fetch photos" });
            }
        }

        [HttpDelete("{photoId}")]
        public async Task<IActionResult> DeletePhoto(long photoId)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _photoService.DeletePhotoAsync(userId, photoId);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error deleting photo");
                return StatusCode(500, new { message = "Failed to delete photo" });
            }
        }

        [HttpPut("{photoId}/set-primary")]
        public async Task<IActionResult> SetPrimaryPhoto(long photoId)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _photoService.SetPrimaryPhotoAsync(userId, photoId);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error setting primary photo");
                return StatusCode(500, new { message = "Failed to set primary photo" });
            }
        }

        [HttpPut("reorder")]
        public async Task<IActionResult> ReorderPhotos([FromBody] List<long> photoIds)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _photoService.ReorderPhotosAsync(userId, photoIds);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error reordering photos");
                return StatusCode(500, new { message = "Failed to reorder photos" });
            }
        }
    }
}
```

---

## 5. MySQL Migration Guide

### Step 1: Install MySQL Packages

```bash
cd InitiateAPI
dotnet remove package Microsoft.Data.SqlClient
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 8.0.0
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### Step 2: Create DbContext

**File:** `Initiate.Infrastructure/Data/ApplicationDbContext.cs`

```csharp
using Microsoft.EntityFrameworkCore;
using Initiate.Domain.Entities;

namespace Initiate.Infrastructure.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        public DbSet<User> Users { get; set; }
        public DbSet<Profile> Profiles { get; set; }
        public DbSet<Photo> Photos { get; set; }
        public DbSet<Like> Likes { get; set; }
        public DbSet<Match> Matches { get; set; }
        public DbSet<Message> Messages { get; set; }
        public DbSet<Interest> Interests { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            // User entity configuration
            modelBuilder.Entity<User>(entity =>
            {
                entity.ToTable("Users");
                entity.HasKey(e => e.Id);
                entity.Property(e => e.MobileNo).IsRequired().HasMaxLength(10);
                entity.HasIndex(e => e.MobileNo).IsUnique();
                entity.Property(e => e.Email).HasMaxLength(255);
                entity.HasIndex(e => e.Email);

                entity.HasOne(e => e.Profile)
                    .WithOne(p => p.User)
                    .HasForeignKey<Profile>(p => p.UserId)
                    .OnDelete(DeleteBehavior.Cascade);

                entity.HasMany(e => e.Photos)
                    .WithOne(p => p.User)
                    .HasForeignKey(p => p.UserId)
                    .OnDelete(DeleteBehavior.Cascade);
            });

            // Profile entity configuration
            modelBuilder.Entity<Profile>(entity =>
            {
                entity.ToTable("Profiles");
                entity.HasKey(e => e.Id);
                entity.Property(e => e.FullName).IsRequired().HasMaxLength(100);
                entity.Property(e => e.Gender).IsRequired().HasMaxLength(10);
                entity.Property(e => e.Bio).HasMaxLength(500);
                entity.Property(e => e.City).IsRequired().HasMaxLength(100);

                // Spatial index for location-based queries
                entity.HasIndex(e => new { e.Latitude, e.Longitude });
            });

            // Photo entity configuration
            modelBuilder.Entity<Photo>(entity =>
            {
                entity.ToTable("Photos");
                entity.HasKey(e => e.Id);
                entity.Property(e => e.PhotoUrl).IsRequired().HasMaxLength(500);
                entity.Property(e => e.ThumbnailUrl).HasMaxLength(500);

                entity.HasIndex(e => new { e.UserId, e.DisplayOrder });
            });

            // Like entity configuration
            modelBuilder.Entity<Like>(entity =>
            {
                entity.ToTable("Likes");
                entity.HasKey(e => e.Id);

                entity.HasOne(e => e.FromUser)
                    .WithMany(u => u.SentLikes)
                    .HasForeignKey(e => e.FromUserId)
                    .OnDelete(DeleteBehavior.Restrict);

                entity.HasOne(e => e.ToUser)
                    .WithMany(u => u.ReceivedLikes)
                    .HasForeignKey(e => e.ToUserId)
                    .OnDelete(DeleteBehavior.Restrict);

                entity.HasIndex(e => new { e.FromUserId, e.ToUserId }).IsUnique();
            });

            // Match entity configuration
            modelBuilder.Entity<Match>(entity =>
            {
                entity.ToTable("Matches");
                entity.HasKey(e => e.Id);

                entity.HasIndex(e => new { e.User1Id, e.User2Id }).IsUnique();
                entity.HasIndex(e => e.MatchedAt);
            });

            // Message entity configuration
            modelBuilder.Entity<Message>(entity =>
            {
                entity.ToTable("Messages");
                entity.HasKey(e => e.Id);
                entity.Property(e => e.Content).IsRequired().HasMaxLength(1000);

                entity.HasIndex(e => new { e.MatchId, e.SentAt });
            });

            // Interest entity configuration
            modelBuilder.Entity<Interest>(entity =>
            {
                entity.ToTable("Interests");
                entity.HasKey(e => e.Id);
                entity.Property(e => e.Name).IsRequired().HasMaxLength(50);
            });

            // Many-to-many: Profile <-> Interest
            modelBuilder.Entity<Profile>()
                .HasMany(p => p.Interests)
                .WithMany(i => i.Profiles)
                .UsingEntity(j => j.ToTable("ProfileInterests"));
        }
    }
}
```

### Step 3: Update Program.cs

```csharp
using Microsoft.EntityFrameworkCore;
using Initiate.Infrastructure.Data;

var builder = WebApplication.CreateBuilder(args);

// Add DbContext with MySQL
builder.Services.AddDbContext<ApplicationDbContext>(options =>
{
    var connectionString = builder.Configuration.GetConnectionString("cs");
    options.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString));
});

// Rest of your configuration...
```

### Step 4: Create Initial Migration

```bash
cd Initiate.Infrastructure
dotnet ef migrations add InitialCreate --startup-project ../InitiateAPI
dotnet ef database update --startup-project ../InitiateAPI
```

### Step 5: Update Connection String

**In User Secrets:**
```bash
dotnet user-secrets set "ConnectionStrings:cs" "Server=localhost;Port=3306;Database=initiate_dating;User=root;Password=your_password;"
```

---

## 6. Redis Caching Implementation

### Step 1: Install Redis Package

```bash
cd InitiateAPI
dotnet add package StackExchange.Redis
dotnet add package Microsoft.Extensions.Caching.StackExchangeRedis
```

### Step 2: Create Redis Service

**File:** `Initiate.Infrastructure/Caching/RedisCacheService.cs`

```csharp
using StackExchange.Redis;
using System.Text.Json;

namespace Initiate.Infrastructure.Caching
{
    public class RedisCacheService : ICacheService
    {
        private readonly IDatabase _database;
        private readonly ILogger<RedisCacheService> _logger;

        public RedisCacheService(IConnectionMultiplexer redis, ILogger<RedisCacheService> logger)
        {
            _database = redis.GetDatabase();
            _logger = logger;
        }

        public async Task<T> GetAsync<T>(string key)
        {
            try
            {
                var value = await _database.StringGetAsync(key);

                if (value.IsNullOrEmpty)
                    return default;

                return JsonSerializer.Deserialize<T>(value);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error getting key {Key} from Redis", key);
                return default;
            }
        }

        public async Task<bool> SetAsync<T>(string key, T value, TimeSpan? expiration = null)
        {
            try
            {
                var serialized = JsonSerializer.Serialize(value);
                return await _database.StringSetAsync(key, serialized, expiration);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error setting key {Key} in Redis", key);
                return false;
            }
        }

        public async Task<bool> RemoveAsync(string key)
        {
            try
            {
                return await _database.KeyDeleteAsync(key);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error removing key {Key} from Redis", key);
                return false;
            }
        }

        public async Task<bool> ExistsAsync(string key)
        {
            try
            {
                return await _database.KeyExistsAsync(key);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error checking existence of key {Key} in Redis", key);
                return false;
            }
        }
    }
}
```

### Step 3: Register Redis in Program.cs

```csharp
using StackExchange.Redis;

// Add Redis
builder.Services.AddSingleton<IConnectionMultiplexer>(sp =>
{
    var configuration = ConfigurationOptions.Parse(
        builder.Configuration.GetConnectionString("Redis"),
        true
    );
    return ConnectionMultiplexer.Connect(configuration);
});

builder.Services.AddScoped<ICacheService, RedisCacheService>();
```

### Step 4: Use Caching in Services

**Example:** Cache profile data

```csharp
public class ProfileService : IProfileService
{
    private readonly IProfileRepository _repository;
    private readonly ICacheService _cache;
    private const string PROFILE_CACHE_KEY = "profile:{0}";
    private static readonly TimeSpan CacheExpiration = TimeSpan.FromHours(1);

    public async Task<ServiceResult<ProfileDto>> GetProfileAsync(long userId)
    {
        // Try to get from cache first
        var cacheKey = string.Format(PROFILE_CACHE_KEY, userId);
        var cached = await _cache.GetAsync<ProfileDto>(cacheKey);

        if (cached != null)
        {
            return ServiceResult<ProfileDto>.Success(cached);
        }

        // If not in cache, get from database
        var profile = await _repository.GetProfileByUserIdAsync(userId);

        if (profile == null)
        {
            return ServiceResult<ProfileDto>.Failure("Profile not found");
        }

        var profileDto = MapToDto(profile);

        // Store in cache
        await _cache.SetAsync(cacheKey, profileDto, CacheExpiration);

        return ServiceResult<ProfileDto>.Success(profileDto);
    }
}
```

---

## 7. Logging Setup (Serilog)

### Step 1: Install Serilog Packages

```bash
cd InitiateAPI
dotnet add package Serilog.AspNetCore
dotnet add package Serilog.Sinks.File
dotnet add package Serilog.Sinks.Console
dotnet add package Serilog.Enrichers.Environment
dotnet add package Serilog.Enrichers.Thread
```

### Step 2: Configure Serilog

**File:** `InitiateAPI/Program.cs`

```csharp
using Serilog;

// Configure Serilog
Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
    .MinimumLevel.Override("Microsoft.Hosting.Lifetime", LogEventLevel.Information)
    .Enrich.FromLogContext()
    .Enrich.WithEnvironmentName()
    .Enrich.WithMachineName()
    .Enrich.WithThreadId()
    .WriteTo.Console()
    .WriteTo.File(
        path: "logs/initiate-api-.txt",
        rollingInterval: RollingInterval.Day,
        retainedFileCountLimit: 30,
        outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj}{NewLine}{Exception}"
    )
    .CreateLogger();

try
{
    Log.Information("Starting Initiate Dating API");

    var builder = WebApplication.CreateBuilder(args);

    // Add Serilog
    builder.Host.UseSerilog();

    // ... rest of your configuration

    var app = builder.Build();

    // Add request logging
    app.UseSerilogRequestLogging();

    app.Run();
}
catch (Exception ex)
{
    Log.Fatal(ex, "Application start-up failed");
}
finally
{
    Log.CloseAndFlush();
}
```

### Step 3: Use Logging in Services

```csharp
public class AuthService : IAuthService
{
    private readonly ILogger<AuthService> _logger;

    public AuthService(ILogger<AuthService> logger)
    {
        _logger = logger;
    }

    public async Task<ServiceResult<string>> SendOtpAsync(SendOtpRequestDto request)
    {
        _logger.LogInformation("Sending OTP to {MobileNo}", request.MobileNo);

        try
        {
            // Your code...

            _logger.LogInformation("OTP sent successfully to {MobileNo}", request.MobileNo);
            return ServiceResult<string>.Success(null, "OTP sent");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to send OTP to {MobileNo}", request.MobileNo);
            throw;
        }
    }
}
```

---

## 8. Input Validation (FluentValidation)

### Step 1: Install FluentValidation

```bash
cd Initiate.Application
dotnet add package FluentValidation
dotnet add package FluentValidation.AspNetCore
```

### Step 2: Create Validators

**File:** `Initiate.Application/Validators/UpdateProfileValidator.cs`

```csharp
using FluentValidation;
using Initiate.Application.DTOs.Profile;

namespace Initiate.Application.Validators
{
    public class UpdateProfileValidator : AbstractValidator<UpdateProfileRequestDto>
    {
        public UpdateProfileValidator()
        {
            RuleFor(x => x.FullName)
                .NotEmpty().WithMessage("Full name is required")
                .Length(2, 100).WithMessage("Name must be between 2 and 100 characters")
                .Matches(@"^[a-zA-Z\s]+$").WithMessage("Name can only contain letters and spaces");

            RuleFor(x => x.Gender)
                .NotEmpty().WithMessage("Gender is required")
                .Must(g => new[] { "Male", "Female", "Other" }.Contains(g))
                .WithMessage("Gender must be Male, Female, or Other");

            RuleFor(x => x.DateOfBirth)
                .NotEmpty().WithMessage("Date of birth is required")
                .Must(BeAValidAge).WithMessage("You must be between 18 and 100 years old");

            RuleFor(x => x.Bio)
                .MaximumLength(500).WithMessage("Bio cannot exceed 500 characters");

            RuleFor(x => x.HeightCm)
                .InclusiveBetween(120, 250).WithMessage("Height must be between 120cm and 250cm");

            RuleFor(x => x.City)
                .NotEmpty().WithMessage("City is required")
                .Length(2, 100).WithMessage("City name must be between 2 and 100 characters");

            RuleFor(x => x.LookingFor)
                .NotEmpty().WithMessage("Looking for preference is required");
        }

        private bool BeAValidAge(DateTime dateOfBirth)
        {
            var age = DateTime.Today.Year - dateOfBirth.Year;
            if (dateOfBirth.Date > DateTime.Today.AddYears(-age)) age--;
            return age >= 18 && age <= 100;
        }
    }
}
```

### Step 3: Register FluentValidation

**File:** `Program.cs`

```csharp
using FluentValidation;
using FluentValidation.AspNetCore;

builder.Services.AddFluentValidationAutoValidation();
builder.Services.AddValidatorsFromAssemblyContaining<UpdateProfileValidator>();
```

---

## 9. Global Exception Handling

### Create Exception Middleware

**File:** `InitiateAPI/Middleware/ExceptionHandlingMiddleware.cs`

```csharp
using System.Net;
using System.Text.Json;
using InitiateAPI.Model;

namespace InitiateAPI.Middleware
{
    public class ExceptionHandlingMiddleware
    {
        private readonly RequestDelegate _next;
        private readonly ILogger<ExceptionHandlingMiddleware> _logger;

        public ExceptionHandlingMiddleware(RequestDelegate next, ILogger<ExceptionHandlingMiddleware> logger)
        {
            _next = next;
            _logger = logger;
        }

        public async Task InvokeAsync(HttpContext context)
        {
            try
            {
                await _next(context);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "An unhandled exception occurred");
                await HandleExceptionAsync(context, ex);
            }
        }

        private static async Task HandleExceptionAsync(HttpContext context, Exception exception)
        {
            context.Response.ContentType = "application/json";
            context.Response.StatusCode = (int)HttpStatusCode.InternalServerError;

            var response = new Response
            {
                IsSuccess = false,
                StatusCode = context.Response.StatusCode,
                Message = "An error occurred while processing your request",
                Data = null
            };

            // In development, include exception details
            if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Development")
            {
                response.Message = exception.Message;
                response.Data = new
                {
                    exception.StackTrace,
                    InnerException = exception.InnerException?.Message
                };
            }

            var json = JsonSerializer.Serialize(response);
            await context.Response.WriteAsync(json);
        }
    }

    public static class ExceptionHandlingMiddlewareExtensions
    {
        public static IApplicationBuilder UseExceptionHandling(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<ExceptionHandlingMiddleware>();
        }
    }
}
```

**Register in Program.cs:**

```csharp
// Add BEFORE UseRouting
app.UseExceptionHandling();
```

---

## 10. Unit Testing Setup

### Step 1: Create Test Project

```bash
cd "C:\Users\bikash.b.kumar\OneDrive - Accenture\Documents\MyDocs\App Designs\Repos\initiate\InitiateAPI"

dotnet new xunit -n Initiate.UnitTests
dotnet sln add Initiate.UnitTests/Initiate.UnitTests.csproj

cd Initiate.UnitTests
dotnet add reference ../Initiate.Application/Initiate.Application.csproj
dotnet add reference ../Initiate.Domain/Initiate.Domain.csproj

dotnet add package Moq
dotnet add package FluentAssertions
dotnet add package xunit.runner.visualstudio
```

### Step 2: Create Sample Tests

**File:** `Initiate.UnitTests/Services/AuthServiceTests.cs`

```csharp
using Xunit;
using Moq;
using FluentAssertions;
using Initiate.Application.Services;
using Initiate.Application.DTOs.Auth;
using Initiate.Domain.Interfaces;
using Microsoft.Extensions.Logging;

namespace Initiate.UnitTests.Services
{
    public class AuthServiceTests
    {
        private readonly Mock<IAuthRepository> _mockAuthRepo;
        private readonly Mock<IJwtTokenService> _mockJwtService;
        private readonly Mock<IOtpService> _mockOtpService;
        private readonly Mock<ILogger<AuthService>> _mockLogger;
        private readonly AuthService _authService;

        public AuthServiceTests()
        {
            _mockAuthRepo = new Mock<IAuthRepository>();
            _mockJwtService = new Mock<IJwtTokenService>();
            _mockOtpService = new Mock<IOtpService>();
            _mockLogger = new Mock<ILogger<AuthService>>();

            _authService = new AuthService(
                _mockAuthRepo.Object,
                _mockJwtService.Object,
                _mockOtpService.Object,
                _mockLogger.Object
            );
        }

        [Fact]
        public async Task SendOtpAsync_WithValidMobileNumber_ReturnsSuccess()
        {
            // Arrange
            var request = new SendOtpRequestDto { MobileNo = "9876543210" };
            _mockOtpService.Setup(x => x.GenerateOtp()).Returns("1234");
            _mockAuthRepo.Setup(x => x.SaveOtpAsync(It.IsAny<string>(), It.IsAny<string>()))
                .ReturnsAsync(true);

            // Act
            var result = await _authService.SendOtpAsync(request);

            // Assert
            result.IsSuccess.Should().BeTrue();
            result.Message.Should().Contain("sent successfully");
            _mockOtpService.Verify(x => x.SendOtpSmsAsync(request.MobileNo, "1234"), Times.Once);
        }

        [Theory]
        [InlineData("123456789")]   // 9 digits
        [InlineData("12345678901")] // 11 digits
        [InlineData("0123456789")]  // starts with 0
        [InlineData("5123456789")]  // starts with 5
        public async Task SendOtpAsync_WithInvalidMobileNumber_ReturnsFailure(string mobileNo)
        {
            // Arrange
            var request = new SendOtpRequestDto { MobileNo = mobileNo };

            // Act
            var result = await _authService.SendOtpAsync(request);

            // Assert
            result.IsSuccess.Should().BeFalse();
            result.Message.Should().Contain("Invalid mobile number");
        }

        [Fact]
        public async Task VerifyOtpAsync_WithValidOtp_ReturnsTokenAndUserInfo()
        {
            // Arrange
            var request = new VerifyOtpRequestDto
            {
                MobileNo = "9876543210",
                Otp = "1234"
            };

            var mockUser = new User
            {
                Id = 1,
                MobileNo = "9876543210",
                Profile = new Profile { IsProfileCompleted = true }
            };

            _mockAuthRepo.Setup(x => x.VerifyOtpAsync(request.MobileNo, request.Otp))
                .ReturnsAsync(mockUser);
            _mockJwtService.Setup(x => x.GenerateToken(mockUser.Id, mockUser.MobileNo))
                .Returns("mock_jwt_token");

            // Act
            var result = await _authService.VerifyOtpAsync(request);

            // Assert
            result.IsSuccess.Should().BeTrue();
            result.Data.Should().NotBeNull();
            result.Data.Token.Should().Be("mock_jwt_token");
            result.Data.IsProfileCompleted.Should().BeTrue();
            result.Data.UserId.Should().Be(1);
        }
    }
}
```

### Step 3: Run Tests

```bash
cd Initiate.UnitTests
dotnet test
```

---

## 11. Complete Code Templates

### Template 1: Complete Controller Template

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Initiate.Application.Interfaces;
using Initiate.Application.DTOs.YourDomain;
using InitiateAPI.Helper;

namespace InitiateAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    [Authorize]
    public class YourController : ControllerBase
    {
        private readonly IYourService _service;
        private readonly ILogger<YourController> _logger;

        public YourController(IYourService service, ILogger<YourController> logger)
        {
            _service = service;
            _logger = logger;
        }

        [HttpGet]
        public async Task<IActionResult> GetAll()
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _service.GetAllAsync(userId);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error in GetAll");
                return StatusCode(500, new { message = "An error occurred" });
            }
        }

        [HttpGet("{id}")]
        public async Task<IActionResult> GetById(long id)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _service.GetByIdAsync(id, userId);

                return result.IsSuccess ? Ok(result) : NotFound(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error in GetById {Id}", id);
                return StatusCode(500, new { message = "An error occurred" });
            }
        }

        [HttpPost]
        public async Task<IActionResult> Create([FromBody] CreateRequestDto request)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _service.CreateAsync(userId, request);

                return result.IsSuccess
                    ? CreatedAtAction(nameof(GetById), new { id = result.Data.Id }, result)
                    : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error in Create");
                return StatusCode(500, new { message = "An error occurred" });
            }
        }

        [HttpPut("{id}")]
        public async Task<IActionResult> Update(long id, [FromBody] UpdateRequestDto request)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _service.UpdateAsync(id, userId, request);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error in Update {Id}", id);
                return StatusCode(500, new { message = "An error occurred" });
            }
        }

        [HttpDelete("{id}")]
        public async Task<IActionResult> Delete(long id)
        {
            try
            {
                var userId = JwtClaimHelper.GetUserId(User);
                var result = await _service.DeleteAsync(id, userId);

                return result.IsSuccess ? Ok(result) : BadRequest(result);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Error in Delete {Id}", id);
                return StatusCode(500, new { message = "An error occurred" });
            }
        }
    }
}
```

---

## 12. Daily Development Checklist

### Monday (Security Fixes)
```
☐ Move credentials to User Secrets
☐ Remove OTP from API response
☐ Update CORS policy
☐ Add input validation to Profile
☐ Add model validation filter
☐ Test all endpoints with Postman
☐ Commit changes to Git
☐ Document changes made
```

### Tuesday (Clean Architecture)
```
☐ Create Domain project
☐ Create Application project
☐ Create Infrastructure project
☐ Add project references
☐ Create all domain entities (User, Profile, Photo, etc.)
☐ Create domain enums
☐ Create application DTOs
☐ Create service interfaces
☐ Test compilation
☐ Commit changes
```

### Wednesday (Photo Upload)
```
☐ Install AWS SDK and ImageSharp
☐ Create PhotoStorageService
☐ Create PhotoController
☐ Implement upload endpoint
☐ Implement delete endpoint
☐ Implement set primary endpoint
☐ Implement reorder endpoint
☐ Test photo upload with Postman
☐ Test file validation (size, type)
☐ Commit changes
```

### Thursday (Logging & Validation)
```
☐ Install Serilog packages
☐ Configure Serilog in Program.cs
☐ Add logging to all services
☐ Install FluentValidation
☐ Create validators for all DTOs
☐ Register validators
☐ Create exception handling middleware
☐ Test error scenarios
☐ Check log files
☐ Commit changes
```

### Friday (Testing & Review)
```
☐ Create unit test project
☐ Install testing packages (xUnit, Moq, FluentAssertions)
☐ Write tests for AuthService
☐ Write tests for ProfileService
☐ Run all tests
☐ Fix any failing tests
☐ Code cleanup (remove unused code)
☐ Update XML documentation
☐ Self code review
☐ Update CODE_REVIEW.md with progress
☐ Prepare demo for Saturday
☐ Commit all changes
```

---

## 13. Postman Collection Examples

### Auth - Send OTP
```http
POST http://localhost:5000/api/auth/sendotp
Content-Type: application/json

{
  "mobileNo": "9876543210"
}
```

### Auth - Verify OTP
```http
POST http://localhost:5000/api/auth/verifyotp
Content-Type: application/json

{
  "mobileNo": "9876543210",
  "otp": "1234"
}
```

### Profile - Get Profile
```http
GET http://localhost:5000/api/profile
Authorization: Bearer YOUR_JWT_TOKEN_HERE
```

### Profile - Update Profile
```http
POST http://localhost:5000/api/profile/saveprofile
Authorization: Bearer YOUR_JWT_TOKEN_HERE
Content-Type: application/json

{
  "fullName": "John Doe",
  "gender": "Male",
  "dob": "1995-05-15",
  "bio": "Software developer from Mumbai",
  "heightCm": 175,
  "city": "Mumbai",
  "lookingFor": "Relationship"
}
```

### Photo - Upload
```http
POST http://localhost:5000/api/photos/upload
Authorization: Bearer YOUR_JWT_TOKEN_HERE
Content-Type: multipart/form-data

file: [SELECT FILE]
```

---

## 14. Troubleshooting Guide

### Issue: User Secrets Not Working

**Solution:**
```bash
# Verify secrets are set
dotnet user-secrets list

# If empty, reinitialize
dotnet user-secrets init
dotnet user-secrets set "ConnectionStrings:cs" "your_connection_string"
```

### Issue: MySQL Connection Failed

**Solution:**
```bash
# Test MySQL connection
mysql -u root -p -h localhost

# Check MySQL service is running
# Windows: services.msc -> MySQL80
# Linux: sudo systemctl status mysql
```

### Issue: Entity Framework Migrations Failed

**Solution:**
```bash
# Remove previous migrations
dotnet ef migrations remove

# Recreate migration
dotnet ef migrations add InitialCreate

# If database exists, drop it first
dotnet ef database drop
dotnet ef database update
```

### Issue: Redis Not Connecting

**Solution:**
```bash
# Check Redis is running
redis-cli ping
# Should return: PONG

# Start Redis (Docker)
docker run -d -p 6379:6379 redis:latest

# Or Windows service
# Start redis-server.exe
```

---

## 15. Week 1 Success Criteria

**By Friday Evening, You Should Have:**

✅ All critical security issues fixed
✅ Clean Architecture implemented (3 projects)
✅ Photo upload working (local or S3)
✅ Serilog logging configured
✅ FluentValidation implemented
✅ Global exception handling
✅ Unit tests (>50% coverage)
✅ All code committed to Git
✅ Demo ready for Saturday review

---

## 16. Resources & References

**Official Documentation:**
- .NET 8: https://learn.microsoft.com/en-us/dotnet/
- Entity Framework Core: https://learn.microsoft.com/en-us/ef/core/
- MySQL: https://dev.mysql.com/doc/
- Redis: https://redis.io/docs/
- AWS S3: https://docs.aws.amazon.com/s3/
- Serilog: https://serilog.net/
- FluentValidation: https://docs.fluentvalidation.net/

**Project Documentation:**
- Review Document: `CODE_REVIEW.md`
- Project Guide: `../InitiateReview/docs/PROJECT_STRUCTURE_AND_GUIDE.md`
- Delivery Plan: `../InitiateReview/docs/6_WEEK_DELIVERY_PLAN.md`

---

## 17. Quick Commands Reference

```bash
# Build solution
dotnet build

# Run API
dotnet run --project InitiateAPI

# Create migration
dotnet ef migrations add MigrationName --startup-project InitiateAPI

# Update database
dotnet ef database update --startup-project InitiateAPI

# Run tests
dotnet test

# Run specific test
dotnet test --filter "FullyQualifiedName~AuthServiceTests"

# Add package
dotnet add package PackageName

# Remove package
dotnet remove package PackageName

# User secrets
dotnet user-secrets set "Key" "Value"
dotnet user-secrets list
dotnet user-secrets remove "Key"
dotnet user-secrets clear

# Git commands
git status
git add .
git commit -m "commit message"
git push
```

---

## 18. Contact & Support

**Need Help?**
- Check CODE_REVIEW.md for detailed analysis
- Review PROJECT_STRUCTURE_AND_GUIDE.md for examples
- Check StackOverflow for technical questions
- Contact: Bikash Kumar

---

**Good luck with Week 1 development! Remember: Fix security issues first, then build features! 🚀**

---

**Document Version:** 1.0
**Last Updated:** January 5, 2026
**Next Update:** After Week 1 completion
