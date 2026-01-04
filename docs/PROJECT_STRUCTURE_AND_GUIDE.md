# Initiate Dating App - Complete Development Guide
## Project Management & Code Review Guide for Non-Technical Leads

**Version:** 1.0
**Author:** Bikash Kumar
**Date:** January 2026
**Tech Stack:** Flutter | .NET Core 8 | MySQL | Redis

---

## Table of Contents
1. [Project Structure Overview](#project-structure-overview)
2. [Understanding the Architecture](#understanding-the-architecture)
3. [Flutter Developer Guide & Review](#flutter-developer-guide--review)
4. [Backend Developer Guide & Review](#backend-developer-guide--review)
5. [Database Developer Guide & Review](#database-developer-guide--review)
6. [Code Review Checklist](#code-review-checklist)
7. [Common Issues & Red Flags](#common-issues--red-flags)
8. [Quality Standards](#quality-standards)
9. [Development Workflow](#development-workflow)
10. [Testing Requirements](#testing-requirements)

---

## 1. Project Structure Overview

### Complete Folder Structure

```
initiate-dating-app/
│
├── mobile/                          # Flutter Mobile Application
│   ├── lib/
│   │   ├── core/                   # Core functionality
│   │   │   ├── constants/          # App constants
│   │   │   ├── theme/              # App theme & colors
│   │   │   ├── utils/              # Utility functions
│   │   │   └── errors/             # Error handling
│   │   │
│   │   ├── data/                   # Data layer
│   │   │   ├── models/             # Data models (User, Match, Message)
│   │   │   ├── repositories/       # Data repositories
│   │   │   ├── datasources/        # API & local data sources
│   │   │   │   ├── remote/         # API calls
│   │   │   │   └── local/          # Local storage (SQLite/SharedPrefs)
│   │   │   └── services/           # External services (APIs)
│   │   │
│   │   ├── domain/                 # Business logic layer
│   │   │   ├── entities/           # Business entities
│   │   │   ├── usecases/           # Business use cases
│   │   │   └── repositories/       # Abstract repositories
│   │   │
│   │   ├── presentation/           # UI layer
│   │   │   ├── screens/            # All app screens
│   │   │   │   ├── auth/           # Login, Register, Verification
│   │   │   │   ├── onboarding/     # Profile setup wizard
│   │   │   │   ├── home/           # Main home screen
│   │   │   │   ├── discovery/      # Swipe interface
│   │   │   │   ├── matches/        # Matches list
│   │   │   │   ├── chat/           # Chat screens
│   │   │   │   ├── stories/        # Story feature
│   │   │   │   ├── profile/        # User profile
│   │   │   │   └── settings/       # App settings
│   │   │   │
│   │   │   ├── widgets/            # Reusable widgets
│   │   │   │   ├── common/         # Common widgets (buttons, cards)
│   │   │   │   └── swipe/          # Swipe card components
│   │   │   │
│   │   │   └── providers/          # State management (Riverpod/Bloc)
│   │   │
│   │   ├── config/                 # Configuration
│   │   │   ├── routes/             # App routing
│   │   │   ├── dependency_injection.dart
│   │   │   └── api_config.dart
│   │   │
│   │   └── main.dart               # App entry point
│   │
│   ├── assets/                     # Assets
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   │
│   ├── test/                       # Unit & widget tests
│   ├── integration_test/           # Integration tests
│   ├── pubspec.yaml                # Dependencies
│   └── README.md
│
├── backend/                        # .NET Core Backend
│   ├── src/
│   │   ├── Initiate.API/           # Web API Project
│   │   │   ├── Controllers/        # API Controllers
│   │   │   │   ├── AuthController.cs
│   │   │   │   ├── ProfileController.cs
│   │   │   │   ├── DiscoveryController.cs
│   │   │   │   ├── MatchController.cs
│   │   │   │   ├── MessageController.cs
│   │   │   │   ├── StoryController.cs
│   │   │   │   └── SubscriptionController.cs
│   │   │   │
│   │   │   ├── Hubs/               # SignalR Hubs
│   │   │   │   ├── ChatHub.cs
│   │   │   │   └── NotificationHub.cs
│   │   │   │
│   │   │   ├── Middleware/         # Custom middleware
│   │   │   │   ├── ExceptionHandlingMiddleware.cs
│   │   │   │   └── RateLimitingMiddleware.cs
│   │   │   │
│   │   │   ├── Filters/            # Action filters
│   │   │   │   └── ValidationFilter.cs
│   │   │   │
│   │   │   ├── Program.cs          # App entry point
│   │   │   └── appsettings.json    # Configuration
│   │   │
│   │   ├── Initiate.Application/   # Application Layer (Business Logic)
│   │   │   ├── Services/           # Business services
│   │   │   │   ├── AuthService.cs
│   │   │   │   ├── ProfileService.cs
│   │   │   │   ├── MatchingService.cs
│   │   │   │   ├── MessageService.cs
│   │   │   │   ├── StoryService.cs
│   │   │   │   └── SubscriptionService.cs
│   │   │   │
│   │   │   ├── DTOs/               # Data Transfer Objects
│   │   │   │   ├── Requests/
│   │   │   │   └── Responses/
│   │   │   │
│   │   │   ├── Interfaces/         # Service interfaces
│   │   │   ├── Validators/         # Input validators
│   │   │   └── Mappings/           # AutoMapper profiles
│   │   │
│   │   ├── Initiate.Domain/        # Domain Layer (Core Business)
│   │   │   ├── Entities/           # Domain entities
│   │   │   │   ├── User.cs
│   │   │   │   ├── Match.cs
│   │   │   │   ├── Message.cs
│   │   │   │   ├── Story.cs
│   │   │   │   ├── Like.cs
│   │   │   │   └── Subscription.cs
│   │   │   │
│   │   │   ├── Enums/              # Enumerations
│   │   │   ├── ValueObjects/       # Value objects
│   │   │   └── Interfaces/         # Repository interfaces
│   │   │
│   │   ├── Initiate.Infrastructure/ # Infrastructure Layer
│   │   │   ├── Data/               # Database context
│   │   │   │   ├── ApplicationDbContext.cs
│   │   │   │   └── Configurations/ # Entity configurations
│   │   │   │
│   │   │   ├── Repositories/       # Repository implementations
│   │   │   │   ├── UserRepository.cs
│   │   │   │   ├── MatchRepository.cs
│   │   │   │   ├── MessageRepository.cs
│   │   │   │   └── StoryRepository.cs
│   │   │   │
│   │   │   ├── Services/           # External service implementations
│   │   │   │   ├── EmailService.cs
│   │   │   │   ├── SmsService.cs
│   │   │   │   ├── S3Service.cs
│   │   │   │   ├── FacialRecognitionService.cs
│   │   │   │   ├── PaymentService.cs
│   │   │   │   └── PushNotificationService.cs
│   │   │   │
│   │   │   └── Caching/            # Redis caching
│   │   │       └── RedisCacheService.cs
│   │   │
│   │   └── Initiate.BackgroundJobs/ # Background jobs
│   │       ├── Jobs/
│   │       │   ├── StoryExpiryJob.cs
│   │       │   ├── ChatCleanupJob.cs
│   │       │   └── DailyLikeResetJob.cs
│   │       └── JobConfiguration.cs
│   │
│   ├── tests/
│   │   ├── Initiate.UnitTests/     # Unit tests
│   │   └── Initiate.IntegrationTests/ # Integration tests
│   │
│   ├── Initiate.sln                # Solution file
│   └── README.md
│
├── database/                       # Database scripts
│   ├── migrations/                 # Migration scripts
│   ├── seeds/                      # Seed data
│   ├── schema.sql                  # Complete schema
│   └── stored_procedures/          # Stored procedures
│
├── docs/                           # Documentation
│   ├── api/                        # API documentation
│   ├── architecture/               # Architecture diagrams
│   └── deployment/                 # Deployment guides
│
├── infrastructure/                 # Infrastructure as Code
│   ├── docker/                     # Docker files
│   │   ├── docker-compose.yml
│   │   ├── Dockerfile.api
│   │   └── Dockerfile.jobs
│   │
│   └── kubernetes/                 # K8s manifests (if needed)
│
└── .github/
    └── workflows/                  # CI/CD pipelines
        ├── flutter-ci.yml
        └── dotnet-ci.yml
```

---

## 2. Understanding the Architecture

### 2.1 High-Level Architecture Explanation

Think of the app like a restaurant:

1. **Flutter App (Frontend)** = Customer Interface
   - The menu (UI) customers see
   - How customers order (user interactions)
   - What customers see (data display)

2. **.NET Core API (Backend)** = Kitchen & Wait Staff
   - Receives orders (API requests)
   - Cooks food (business logic)
   - Delivers food (API responses)

3. **Database (MySQL)** = Pantry & Storage
   - Stores all ingredients (user data, messages, matches)
   - Organized shelves (tables with indexes)

4. **Redis Cache** = Quick Access Counter
   - Frequently used items kept nearby
   - Fast retrieval without going to pantry

5. **SignalR** = Walkie-Talkie System
   - Real-time communication
   - Instant updates without asking

### 2.2 How Data Flows

**Example: User Sends a Message**

```
┌─────────────┐
│  Flutter    │  1. User types message & hits send
│     App     │     sendMessage("Hello!")
└──────┬──────┘
       │
       │ 2. HTTP POST to API
       │    POST /api/v1/messages
       ▼
┌─────────────┐
│   .NET      │  3. API receives request
│     API     │     - Validates user is logged in
└──────┬──────┘     - Checks if match exists
       │            - Applies female-first rule
       │
       │ 4. If valid, process message
       ▼
┌─────────────┐
│   MySQL     │  5. Save message to database
│  Database   │     INSERT INTO messages...
└──────┬──────┘
       │
       │ 6. Message saved successfully
       ▼
┌─────────────┐
│  SignalR    │  7. Send real-time notification
│     Hub     │     to receiver's phone instantly
└─────────────┘
```

### 2.3 Clean Architecture Layers

**Why layers matter:** Like a building, each floor has a purpose. You can't skip floors!

```
┌─────────────────────────────────────────┐
│  PRESENTATION (Flutter UI)              │  ← What users see
│  - Screens, Widgets, User Input         │
└───────────────┬─────────────────────────┘
                │
┌───────────────▼─────────────────────────┐
│  APPLICATION (Business Logic)           │  ← What the app does
│  - Services, Use Cases, DTOs            │
└───────────────┬─────────────────────────┘
                │
┌───────────────▼─────────────────────────┐
│  DOMAIN (Core Business Rules)           │  ← Business rules
│  - Entities, Interfaces, Enums          │
└───────────────┬─────────────────────────┘
                │
┌───────────────▼─────────────────────────┐
│  INFRASTRUCTURE (External Services)     │  ← Technical details
│  - Database, APIs, File Storage         │
└─────────────────────────────────────────┘
```

**Review Tip:** If your developer writes database code in the UI layer, that's wrong! Each layer should only talk to the layer directly below it.

---

## 3. Flutter Developer Guide & Review

### 3.1 What Your Flutter Developer Should Deliver

#### Phase 1 Deliverables (Week 1-4)
- [ ] Project setup with clean architecture
- [ ] Authentication screens (Login, Register, OTP)
- [ ] API integration setup
- [ ] State management setup (Riverpod/Bloc)

#### Phase 2 Deliverables (Week 5-8)
- [ ] Onboarding wizard
- [ ] Profile creation & photo upload
- [ ] Camera integration for selfie verification

#### Phase 3 Deliverables (Week 9-12)
- [ ] Swipe interface with animations
- [ ] Match logic integration
- [ ] Basic chat functionality

#### Phase 4 Deliverables (Week 13-16)
- [ ] Story feature
- [ ] SignalR real-time chat
- [ ] Push notifications
- [ ] Premium subscription flows

### 3.2 Code Review Checklist for Flutter

#### ✅ Project Structure
**What to check:**
```dart
// GOOD: Proper folder structure
lib/
  ├── core/
  ├── data/
  ├── domain/
  ├── presentation/
  └── config/

// BAD: Everything in one folder
lib/
  ├── screen1.dart
  ├── screen2.dart
  ├── api.dart
  └── utils.dart
```

**Questions to ask:**
- ✅ Are files organized by feature or layer?
- ✅ Is there separation between UI and business logic?
- ❌ Are there files with 1000+ lines of code? (RED FLAG)

---

#### ✅ State Management

**GOOD Example (Riverpod):**
```dart
// 1. Define provider
final userProvider = StateNotifierProvider<UserNotifier, User?>((ref) {
  return UserNotifier(ref.read(userRepositoryProvider));
});

// 2. Notifier with logic
class UserNotifier extends StateNotifier<User?> {
  final UserRepository _repository;

  UserNotifier(this._repository) : super(null);

  Future<void> loadUser() async {
    try {
      final user = await _repository.getCurrentUser();
      state = user;
    } catch (e) {
      // Handle error
    }
  }
}

// 3. Use in widget
class ProfileScreen extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final user = ref.watch(userProvider);

    if (user == null) {
      return LoadingWidget();
    }

    return Text(user.name);
  }
}
```

**BAD Example:**
```dart
// ❌ BAD: Direct API call in widget
class ProfileScreen extends StatefulWidget {
  @override
  _ProfileScreenState createState() => _ProfileScreenState();
}

class _ProfileScreenState extends State<ProfileScreen> {
  User? user;

  @override
  void initState() {
    super.initState();
    // ❌ API call directly in widget
    http.get('https://api.com/user').then((response) {
      setState(() {
        user = User.fromJson(response.body);
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return Text(user?.name ?? '');
  }
}
```

**Review Questions:**
- ✅ Is state managed using Riverpod or Bloc? (Not setState everywhere)
- ✅ Are API calls in repositories, not in widgets?
- ✅ Is loading/error state handled properly?

---

#### ✅ API Integration

**GOOD Example:**
```dart
// 1. API Service with proper error handling
class AuthApiService {
  final Dio _dio;

  AuthApiService(this._dio);

  Future<Either<Failure, User>> login(String email, String password) async {
    try {
      final response = await _dio.post(
        '/api/v1/auth/login',
        data: {'email': email, 'password': password},
      );

      if (response.statusCode == 200) {
        return Right(User.fromJson(response.data));
      } else {
        return Left(ServerFailure('Login failed'));
      }
    } on DioException catch (e) {
      return Left(NetworkFailure(e.message));
    } catch (e) {
      return Left(UnknownFailure(e.toString()));
    }
  }
}

// 2. Repository pattern
class AuthRepository {
  final AuthApiService _apiService;
  final LocalStorageService _localStorage;

  AuthRepository(this._apiService, this._localStorage);

  Future<Either<Failure, User>> login(String email, String password) async {
    final result = await _apiService.login(email, password);

    return result.fold(
      (failure) => Left(failure),
      (user) async {
        await _localStorage.saveUser(user);
        return Right(user);
      },
    );
  }
}
```

**Review Questions:**
- ✅ Are API endpoints defined in constants file?
- ✅ Is error handling implemented (try-catch)?
- ✅ Are responses parsed into model objects?
- ✅ Is there retry logic for failed requests?
- ✅ Are auth tokens managed properly?

---

#### ✅ UI/UX Quality

**GOOD Example:**
```dart
// Responsive design with proper constraints
class SwipeCard extends StatelessWidget {
  final UserProfile profile;

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        return Container(
          width: constraints.maxWidth * 0.9,
          height: constraints.maxHeight * 0.7,
          decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(16),
            boxShadow: [
              BoxShadow(
                color: Colors.black26,
                blurRadius: 10,
                offset: Offset(0, 4),
              ),
            ],
          ),
          child: Stack(
            children: [
              // Image with loading and error states
              CachedNetworkImage(
                imageUrl: profile.photoUrl,
                fit: BoxFit.cover,
                placeholder: (context, url) => ShimmerLoading(),
                errorWidget: (context, url, error) => Icon(Icons.error),
              ),

              // Gradient overlay for text readability
              Positioned(
                bottom: 0,
                left: 0,
                right: 0,
                child: Container(
                  decoration: BoxDecoration(
                    gradient: LinearGradient(
                      begin: Alignment.topCenter,
                      end: Alignment.bottomCenter,
                      colors: [
                        Colors.transparent,
                        Colors.black.withOpacity(0.7),
                      ],
                    ),
                  ),
                  padding: EdgeInsets.all(16),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        '${profile.name}, ${profile.age}',
                        style: Theme.of(context).textTheme.headlineMedium?.copyWith(
                          color: Colors.white,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                      SizedBox(height: 8),
                      Row(
                        children: [
                          Icon(Icons.location_on, color: Colors.white, size: 16),
                          SizedBox(width: 4),
                          Text(
                            '${profile.distanceKm.toStringAsFixed(1)} km away',
                            style: TextStyle(color: Colors.white),
                          ),
                        ],
                      ),
                    ],
                  ),
                ),
              ),
            ],
          ),
        );
      },
    );
  }
}
```

**Review Questions:**
- ✅ Does the UI match the design mockups?
- ✅ Are loading states shown (spinners, skeletons)?
- ✅ Are error states handled (error messages, retry buttons)?
- ✅ Is the app responsive on different screen sizes?
- ✅ Are animations smooth (60 FPS)?
- ✅ Are images cached and optimized?

---

#### ✅ Critical Features Implementation

**1. Swipe Functionality**
```dart
class SwipeController extends StateNotifier<SwipeState> {
  final DiscoveryRepository _repository;

  SwipeController(this._repository) : super(SwipeState.initial());

  Future<void> swipeRight(UserProfile profile) async {
    // Optimistic update
    state = state.copyWith(isLoading: true);

    final result = await _repository.likeUser(profile.id);

    result.fold(
      (failure) {
        state = state.copyWith(
          isLoading: false,
          error: failure.message,
        );
      },
      (swipeResult) {
        if (swipeResult.isMatch) {
          // Show match dialog
          state = state.copyWith(
            isLoading: false,
            matchedUser: profile,
            showMatchDialog: true,
          );
        } else {
          // Load next profile
          state = state.copyWith(isLoading: false);
          _loadNextProfile();
        }
      },
    );
  }

  Future<void> swipeLeft(UserProfile profile) async {
    state = state.copyWith(isLoading: true);

    await _repository.passUser(profile.id);

    state = state.copyWith(isLoading: false);
    _loadNextProfile();
  }
}
```

**Review Checklist:**
- ✅ Does swipe animation work smoothly?
- ✅ Is match dialog shown on mutual like?
- ✅ Does it handle API errors gracefully?
- ✅ Is the daily limit enforced (25 likes for free users)?

---

**2. Real-Time Chat with SignalR**
```dart
class ChatService {
  HubConnection? _hubConnection;

  Future<void> connect(String token) async {
    _hubConnection = HubConnectionBuilder()
        .withUrl(
          '${ApiConfig.baseUrl}/hubs/chat',
          HttpConnectionOptions(
            accessTokenFactory: () async => token,
          ),
        )
        .withAutomaticReconnect()
        .build();

    // Listen for incoming messages
    _hubConnection?.on('ReceiveMessage', (message) {
      final msg = Message.fromJson(message?[0]);
      _handleIncomingMessage(msg);
    });

    await _hubConnection?.start();
  }

  Future<void> sendMessage(int receiverId, String content) async {
    if (_hubConnection?.state == HubConnectionState.connected) {
      await _hubConnection?.invoke(
        'SendMessage',
        args: [receiverId, content],
      );
    }
  }

  void dispose() {
    _hubConnection?.stop();
  }
}
```

**Review Checklist:**
- ✅ Does SignalR connection reconnect automatically?
- ✅ Are messages delivered instantly?
- ✅ Is typing indicator working?
- ✅ Are read receipts implemented?

---

**3. Location Services**
```dart
class LocationService {
  final Geolocator _geolocator;

  Future<Either<Failure, Position>> getCurrentLocation() async {
    // Check permissions
    final permission = await Geolocator.checkPermission();

    if (permission == LocationPermission.denied) {
      final requested = await Geolocator.requestPermission();
      if (requested == LocationPermission.denied) {
        return Left(PermissionFailure('Location permission denied'));
      }
    }

    try {
      final position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high,
      );
      return Right(position);
    } catch (e) {
      return Left(LocationFailure(e.toString()));
    }
  }

  Future<void> updateUserLocation() async {
    final result = await getCurrentLocation();

    result.fold(
      (failure) => print('Location error: ${failure.message}'),
      (position) async {
        await _apiService.updateLocation(
          position.latitude,
          position.longitude,
        );
      },
    );
  }
}
```

**Review Checklist:**
- ✅ Is location permission requested properly?
- ✅ Is location updated periodically (every 10 minutes)?
- ✅ Does it work in background?
- ✅ Is battery consumption optimized?

---

### 3.3 Common Flutter Issues to Watch For

#### ❌ RED FLAGS (Tell developer to fix immediately)

1. **Memory Leaks**
```dart
// ❌ BAD: Controllers not disposed
class MyWidget extends StatefulWidget {
  @override
  _MyWidgetState createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  final TextEditingController _controller = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return TextField(controller: _controller);
  }

  // ❌ MISSING dispose() method - MEMORY LEAK!
}

// ✅ GOOD: Proper cleanup
class _MyWidgetState extends State<MyWidget> {
  final TextEditingController _controller = TextEditingController();

  @override
  void dispose() {
    _controller.dispose(); // ✅ Clean up
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return TextField(controller: _controller);
  }
}
```

2. **Hardcoded Values**
```dart
// ❌ BAD: Hardcoded API URL
final response = await http.get('https://api.initiate.com/users');

// ✅ GOOD: Use constants
class ApiConfig {
  static const String baseUrl = 'https://api.initiate.com';
  static const String usersEndpoint = '/users';
}

final response = await http.get('${ApiConfig.baseUrl}${ApiConfig.usersEndpoint}');
```

3. **No Error Handling**
```dart
// ❌ BAD: No error handling
Future<void> loadData() async {
  final response = await http.get(url);
  final data = json.decode(response.body);
  setState(() {
    users = data;
  });
}

// ✅ GOOD: Proper error handling
Future<void> loadData() async {
  try {
    setState(() {
      isLoading = true;
      error = null;
    });

    final response = await http.get(url);

    if (response.statusCode == 200) {
      final data = json.decode(response.body);
      setState(() {
        users = data;
        isLoading = false;
      });
    } else {
      setState(() {
        error = 'Failed to load data';
        isLoading = false;
      });
    }
  } catch (e) {
    setState(() {
      error = e.toString();
      isLoading = false;
    });
  }
}
```

4. **Blocking UI Thread**
```dart
// ❌ BAD: Heavy computation on UI thread
void processImages(List<File> images) {
  for (var image in images) {
    final bytes = image.readAsBytesSync(); // ❌ Blocks UI
    final compressed = compressImage(bytes); // ❌ Blocks UI
  }
}

// ✅ GOOD: Use isolates for heavy work
Future<void> processImages(List<File> images) async {
  await compute(_compressImages, images); // ✅ Runs in background
}

List<Uint8List> _compressImages(List<File> images) {
  // Heavy work happens in separate isolate
  return images.map((img) {
    final bytes = img.readAsBytesSync();
    return compressImage(bytes);
  }).toList();
}
```

---

### 3.4 How to Test Flutter Code (Without Being a Developer)

#### Manual Testing Checklist

**Authentication Flow:**
1. Open app → Should see login/register screen
2. Try registering with invalid email → Should show error
3. Register with valid details → Should receive OTP
4. Enter wrong OTP → Should show error
5. Enter correct OTP → Should proceed to onboarding

**Swipe Feature:**
1. Swipe right on 5 profiles → Should work smoothly
2. On 26th swipe (free user) → Should show "Limit reached" message
3. Match happens → Should show match celebration dialog
4. Swipe left → Profile should disappear

**Chat Feature:**
1. Send message → Should appear instantly
2. Receive message → Should get notification
3. Turn off internet → Should show "Connecting..." status
4. Turn on internet → Should reconnect automatically

**Performance:**
1. Scroll through profiles → Should be smooth (no stuttering)
2. Upload photo → Should compress and upload in < 5 seconds
3. Open app from notification → Should open correct chat

---

## 4. Backend Developer Guide & Review

### 4.1 What Your Backend Developer Should Deliver

#### Phase 1 Deliverables (Week 1-4)
- [ ] Project setup with Clean Architecture
- [ ] Database schema implementation
- [ ] Authentication APIs (Register, Login, OTP)
- [ ] JWT token implementation
- [ ] Basic CRUD for User profiles

#### Phase 2 Deliverables (Week 5-8)
- [ ] Selfie verification integration (AWS Rekognition)
- [ ] Discovery API with proximity search
- [ ] Swipe API (like/pass)
- [ ] Match detection logic
- [ ] Free tier limit enforcement (25 likes/day)

#### Phase 3 Deliverables (Week 9-12)
- [ ] SignalR chat implementation
- [ ] Message CRUD operations
- [ ] Female-first messaging logic
- [ ] Read receipts
- [ ] Chat expiry background job

#### Phase 4 Deliverables (Week 13-16)
- [ ] Story feature APIs
- [ ] Story expiry background job
- [ ] Subscription/payment integration
- [ ] Push notification service
- [ ] Admin APIs

### 4.2 Code Review Checklist for .NET Core

#### ✅ Project Structure

**GOOD Example:**
```
Initiate.API/
├── Controllers/        ✅ Thin controllers, no business logic
├── Middleware/         ✅ Cross-cutting concerns
└── Program.cs

Initiate.Application/
├── Services/           ✅ Business logic here
├── DTOs/              ✅ Data transfer objects
└── Interfaces/        ✅ Abstractions

Initiate.Domain/
├── Entities/          ✅ Core business entities
└── Interfaces/        ✅ Repository contracts

Initiate.Infrastructure/
├── Data/              ✅ Database context
├── Repositories/      ✅ Data access
└── Services/          ✅ External integrations
```

**Review Questions:**
- ✅ Is business logic in Application layer, not Controllers?
- ✅ Are entities in Domain layer separate from DTOs?
- ✅ Is database code in Infrastructure layer?

---

#### ✅ Controller Design

**GOOD Example:**
```csharp
[ApiController]
[Route("api/v1/[controller]")]
public class DiscoveryController : ControllerBase
{
    private readonly IMatchingService _matchingService;
    private readonly ILogger<DiscoveryController> _logger;

    public DiscoveryController(
        IMatchingService matchingService,
        ILogger<DiscoveryController> logger)
    {
        _matchingService = matchingService;
        _logger = logger;
    }

    /// <summary>
    /// Get nearby user profiles for discovery feed
    /// </summary>
    [HttpGet("profiles")]
    [Authorize] // ✅ Requires authentication
    [ProducesResponseType(typeof(List<UserProfileDto>), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status401Unauthorized)]
    public async Task<IActionResult> GetNearbyProfiles(
        [FromQuery] int radiusKm = 50)
    {
        try
        {
            var userId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier)?.Value);

            // ✅ Business logic in service, not controller
            var profiles = await _matchingService.GetNearbyProfiles(userId, radiusKm);

            return Ok(profiles);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error fetching nearby profiles");
            return StatusCode(500, "An error occurred");
        }
    }

    /// <summary>
    /// Process swipe action (like or pass)
    /// </summary>
    [HttpPost("swipe")]
    [Authorize]
    [ProducesResponseType(typeof(SwipeResultDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ErrorDto), StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> Swipe([FromBody] SwipeRequestDto request)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        var userId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier)?.Value);

        var result = await _matchingService.ProcessSwipe(
            userId,
            request.TargetUserId,
            request.IsLike
        );

        if (!result.Success)
            return BadRequest(new ErrorDto { Message = result.Message });

        return Ok(result);
    }
}
```

**BAD Example:**
```csharp
// ❌ BAD: Business logic in controller
[HttpPost("swipe")]
public async Task<IActionResult> Swipe([FromBody] SwipeRequestDto request)
{
    var userId = GetUserId();

    // ❌ Database query directly in controller
    var user = await _context.Users.FindAsync(userId);

    // ❌ Business logic in controller
    if (!user.IsPremium)
    {
        var todayLikes = await _context.Likes
            .Where(l => l.FromUserId == userId && l.LikedAt.Date == DateTime.UtcNow.Date)
            .CountAsync();

        if (todayLikes >= 25)
            return BadRequest("Daily limit reached");
    }

    // ❌ Too much code in controller...
    var like = new Like { FromUserId = userId, ToUserId = request.TargetUserId };
    _context.Likes.Add(like);
    await _context.SaveChangesAsync();

    // ❌ Match logic in controller...
    var reciprocal = await _context.Likes
        .FirstOrDefaultAsync(l => l.FromUserId == request.TargetUserId && l.ToUserId == userId);

    if (reciprocal != null)
    {
        var match = new Match { User1Id = userId, User2Id = request.TargetUserId };
        _context.Matches.Add(match);
        await _context.SaveChangesAsync();
    }

    return Ok();
}
```

**Review Questions:**
- ✅ Is controller thin (< 50 lines per action)?
- ✅ Is business logic delegated to services?
- ✅ Are proper HTTP status codes returned?
- ✅ Is authentication/authorization applied?
- ✅ Are DTOs used instead of entities?

---

#### ✅ Service Layer (Business Logic)

**GOOD Example:**
```csharp
public class MatchingService : IMatchingService
{
    private readonly IUserRepository _userRepository;
    private readonly ILikeRepository _likeRepository;
    private readonly IMatchRepository _matchRepository;
    private readonly IRedisCacheService _cacheService;
    private readonly INotificationService _notificationService;
    private readonly ILogger<MatchingService> _logger;

    public MatchingService(
        IUserRepository userRepository,
        ILikeRepository likeRepository,
        IMatchRepository matchRepository,
        IRedisCacheService cacheService,
        INotificationService notificationService,
        ILogger<MatchingService> logger)
    {
        _userRepository = userRepository;
        _likeRepository = likeRepository;
        _matchRepository = matchRepository;
        _cacheService = cacheService;
        _notificationService = notificationService;
        _logger = logger;
    }

    public async Task<SwipeResultDto> ProcessSwipe(
        int userId,
        int targetUserId,
        bool isLike)
    {
        // ✅ Validate business rules
        var user = await _userRepository.GetByIdAsync(userId);
        if (user == null)
            throw new NotFoundException("User not found");

        // ✅ Check if already swiped
        var existingLike = await _likeRepository.GetLikeAsync(userId, targetUserId);
        if (existingLike != null)
            return new SwipeResultDto
            {
                Success = false,
                Message = "Already swiped on this user"
            };

        // ✅ Enforce free tier limits
        if (!user.IsPremium && isLike)
        {
            var canLike = await CheckDailyLikeLimit(userId);
            if (!canLike)
            {
                return new SwipeResultDto
                {
                    Success = false,
                    Message = "Daily like limit reached. Upgrade to Premium!",
                    ShouldShowPremiumUpsell = true
                };
            }
        }

        if (!isLike)
        {
            // Just pass, no action needed
            return new SwipeResultDto { Success = true, IsMatch = false };
        }

        // ✅ Create like
        await _likeRepository.CreateAsync(new Like
        {
            FromUserId = userId,
            ToUserId = targetUserId,
            LikedAt = DateTime.UtcNow
        });

        // ✅ Increment daily counter in Redis
        if (!user.IsPremium)
        {
            await IncrementDailyLikeCount(userId);
        }

        // ✅ Check for mutual match
        var reciprocalLike = await _likeRepository.GetLikeAsync(targetUserId, userId);

        if (reciprocalLike != null)
        {
            // ✅ It's a match! Create match record
            var match = await _matchRepository.CreateAsync(new Match
            {
                User1Id = userId,
                User2Id = targetUserId,
                MatchedAt = DateTime.UtcNow
            });

            // ✅ Send notifications to both users
            await _notificationService.SendMatchNotification(userId, targetUserId);
            await _notificationService.SendMatchNotification(targetUserId, userId);

            _logger.LogInformation("Match created between {User1} and {User2}",
                userId, targetUserId);

            return new SwipeResultDto
            {
                Success = true,
                IsMatch = true,
                MatchId = match.Id
            };
        }

        // ✅ Like recorded but no match yet
        return new SwipeResultDto { Success = true, IsMatch = false };
    }

    public async Task<List<UserProfileDto>> GetNearbyProfiles(
        int userId,
        int radiusKm = 50)
    {
        // ✅ Try cache first
        var cacheKey = $"nearby_profiles:{userId}:{radiusKm}";
        var cachedProfiles = await _cacheService.GetAsync<List<UserProfileDto>>(cacheKey);

        if (cachedProfiles != null)
        {
            _logger.LogDebug("Returning cached nearby profiles for user {UserId}", userId);
            return cachedProfiles;
        }

        // ✅ Get user's location
        var user = await _userRepository.GetByIdAsync(userId);
        if (user == null)
            throw new NotFoundException("User not found");

        // ✅ Get nearby profiles using spatial query
        var profiles = await _userRepository.GetNearbyUsersAsync(
            user.Latitude,
            user.Longitude,
            radiusKm,
            userId
        );

        // ✅ Filter out already swiped users
        var likedUserIds = await _likeRepository.GetLikedUserIdsAsync(userId);
        profiles = profiles.Where(p => !likedUserIds.Contains(p.Id)).ToList();

        // ✅ Apply matching algorithm (prioritize verified, nearby, similar interests)
        profiles = ApplyMatchingAlgorithm(user, profiles);

        // ✅ Convert to DTOs
        var profileDtos = profiles.Select(p => new UserProfileDto
        {
            Id = p.Id,
            Name = p.Name,
            Age = p.Age,
            Photos = p.Photos.Select(ph => ph.Url).ToList(),
            Bio = p.Bio,
            Profession = p.Profession,
            DistanceKm = CalculateDistance(
                user.Latitude, user.Longitude,
                p.Latitude, p.Longitude
            ),
            IsVerified = p.IsVerified,
            Interests = p.Interests.Split(',').ToList()
        }).ToList();

        // ✅ Cache for 10 minutes
        await _cacheService.SetAsync(cacheKey, profileDtos, TimeSpan.FromMinutes(10));

        return profileDtos;
    }

    private async Task<bool> CheckDailyLikeLimit(int userId)
    {
        var today = DateTime.UtcNow.ToString("yyyy-MM-dd");
        var cacheKey = $"daily_likes:{userId}:{today}";

        var count = await _cacheService.GetAsync<int>(cacheKey);
        return count < 25;
    }

    private async Task IncrementDailyLikeCount(int userId)
    {
        var today = DateTime.UtcNow.ToString("yyyy-MM-dd");
        var cacheKey = $"daily_likes:{userId}:{today}";

        await _cacheService.IncrementAsync(cacheKey);

        // Set expiry at midnight
        var midnight = DateTime.UtcNow.Date.AddDays(1);
        var ttl = midnight - DateTime.UtcNow;
        await _cacheService.ExpireAsync(cacheKey, ttl);
    }

    private List<User> ApplyMatchingAlgorithm(User currentUser, List<User> candidates)
    {
        // ✅ Priority scoring
        return candidates
            .Select(c => new
            {
                User = c,
                Score = CalculateMatchScore(currentUser, c)
            })
            .OrderByDescending(x => x.Score)
            .Select(x => x.User)
            .ToList();
    }

    private int CalculateMatchScore(User current, User candidate)
    {
        int score = 0;

        // Verified users get priority
        if (candidate.IsVerified) score += 100;

        // Closer distance = higher score
        var distance = CalculateDistance(
            current.Latitude, current.Longitude,
            candidate.Latitude, candidate.Longitude
        );
        score += (int)(50 - distance); // Up to 50 points for very close

        // Recently active users
        var hoursSinceActive = (DateTime.UtcNow - candidate.LastActive).TotalHours;
        if (hoursSinceActive < 1) score += 30;
        else if (hoursSinceActive < 24) score += 15;

        // Interest matching
        var currentInterests = current.Interests.Split(',');
        var candidateInterests = candidate.Interests.Split(',');
        var commonInterests = currentInterests.Intersect(candidateInterests).Count();
        score += commonInterests * 10;

        return score;
    }

    private double CalculateDistance(double lat1, double lon1, double lat2, double lon2)
    {
        // Haversine formula implementation
        var R = 6371; // Earth's radius in km
        var dLat = ToRadians(lat2 - lat1);
        var dLon = ToRadians(lon2 - lon1);

        var a = Math.Sin(dLat / 2) * Math.Sin(dLat / 2) +
                Math.Cos(ToRadians(lat1)) * Math.Cos(ToRadians(lat2)) *
                Math.Sin(dLon / 2) * Math.Sin(dLon / 2);

        var c = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
        return R * c;
    }

    private double ToRadians(double degrees) => degrees * Math.PI / 180;
}
```

**Review Questions:**
- ✅ Is all business logic in services, not controllers?
- ✅ Are dependencies injected through constructor?
- ✅ Is caching used for expensive operations?
- ✅ Are business rules clearly documented?
- ✅ Is error handling implemented?
- ✅ Are operations logged appropriately?

---

#### ✅ Repository Pattern

**GOOD Example:**
```csharp
public interface IUserRepository
{
    Task<User?> GetByIdAsync(int id);
    Task<User?> GetByEmailAsync(string email);
    Task<List<User>> GetNearbyUsersAsync(
        double latitude,
        double longitude,
        int radiusKm,
        int excludeUserId
    );
    Task<User> CreateAsync(User user);
    Task UpdateAsync(User user);
    Task DeleteAsync(int id);
}

public class UserRepository : IUserRepository
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<UserRepository> _logger;

    public UserRepository(
        ApplicationDbContext context,
        ILogger<UserRepository> logger)
    {
        _context = context;
        _logger = logger;
    }

    public async Task<User?> GetByIdAsync(int id)
    {
        return await _context.Users
            .Include(u => u.Photos)
            .FirstOrDefaultAsync(u => u.Id == id);
    }

    public async Task<List<User>> GetNearbyUsersAsync(
        double latitude,
        double longitude,
        int radiusKm,
        int excludeUserId)
    {
        // ✅ Use raw SQL for spatial queries (MySQL ST_Distance_Sphere)
        var sql = @"
            SELECT u.*,
                   ST_Distance_Sphere(
                       POINT(u.longitude, u.latitude),
                       POINT(@longitude, @latitude)
                   ) / 1000 AS DistanceKm
            FROM users u
            LEFT JOIN user_blocks b1 ON b1.blocker_id = @userId AND b1.blocked_id = u.id
            LEFT JOIN user_blocks b2 ON b2.blocker_id = u.id AND b2.blocked_id = @userId
            WHERE u.id != @userId
              AND b1.id IS NULL
              AND b2.id IS NULL
              AND u.is_verified = 1
            HAVING DistanceKm <= @radiusKm
            ORDER BY u.is_verified DESC, DistanceKm ASC, u.last_active DESC
            LIMIT 50";

        var users = await _context.Users
            .FromSqlRaw(sql,
                new MySqlParameter("@latitude", latitude),
                new MySqlParameter("@longitude", longitude),
                new MySqlParameter("@radiusKm", radiusKm),
                new MySqlParameter("@userId", excludeUserId))
            .Include(u => u.Photos)
            .ToListAsync();

        return users;
    }

    public async Task<User> CreateAsync(User user)
    {
        _context.Users.Add(user);
        await _context.SaveChangesAsync();
        return user;
    }

    public async Task UpdateAsync(User user)
    {
        _context.Users.Update(user);
        await _context.SaveChangesAsync();
    }
}
```

**Review Questions:**
- ✅ Are database operations abstracted behind interfaces?
- ✅ Is Entity Framework used correctly (Include for eager loading)?
- ✅ Are raw SQL queries parameterized (prevent SQL injection)?
- ✅ Are indexes utilized for performance?

---

#### ✅ SignalR Implementation

**GOOD Example:**
```csharp
[Authorize]
public class ChatHub : Hub
{
    private readonly IMessageService _messageService;
    private readonly IMatchRepository _matchRepository;
    private readonly ILogger<ChatHub> _logger;

    public ChatHub(
        IMessageService messageService,
        IMatchRepository matchRepository,
        ILogger<ChatHub> logger)
    {
        _messageService = messageService;
        _matchRepository = matchRepository;
        _logger = logger;
    }

    public override async Task OnConnectedAsync()
    {
        var userId = Context.User?.FindFirst(ClaimTypes.NameIdentifier)?.Value;

        if (userId != null)
        {
            // ✅ Add user to their personal group
            await Groups.AddToGroupAsync(Context.ConnectionId, $"user_{userId}");

            _logger.LogInformation("User {UserId} connected to chat hub", userId);
        }

        await base.OnConnectedAsync();
    }

    public async Task SendMessage(int receiverId, string content)
    {
        var senderIdStr = Context.User?.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (senderIdStr == null)
        {
            throw new HubException("Unauthorized");
        }

        var senderId = int.Parse(senderIdStr);

        try
        {
            // ✅ Apply business rules (female-first messaging)
            var result = await _messageService.SendMessage(senderId, receiverId, content);

            if (!result.Success)
            {
                // ✅ Send error back to sender
                await Clients.Caller.SendAsync("MessageError", result.Message);
                return;
            }

            // ✅ Send message to receiver in real-time
            await Clients.Group($"user_{receiverId}").SendAsync("ReceiveMessage", new
            {
                Id = result.MessageId,
                SenderId = senderId,
                Content = content,
                SentAt = DateTime.UtcNow,
                IsRead = false
            });

            // ✅ Confirm delivery to sender
            await Clients.Caller.SendAsync("MessageDelivered", new
            {
                Id = result.MessageId,
                Status = "delivered"
            });

            _logger.LogInformation("Message sent from {Sender} to {Receiver}",
                senderId, receiverId);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error sending message");
            await Clients.Caller.SendAsync("MessageError", "Failed to send message");
        }
    }

    public async Task MarkAsRead(int messageId)
    {
        var userIdStr = Context.User?.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (userIdStr == null) return;

        var userId = int.Parse(userIdStr);

        await _messageService.MarkAsRead(messageId, userId);

        // ✅ Notify sender that message was read
        var message = await _messageService.GetMessageById(messageId);
        if (message != null)
        {
            await Clients.Group($"user_{message.SenderId}").SendAsync("MessageRead", new
            {
                MessageId = messageId,
                ReadAt = DateTime.UtcNow
            });
        }
    }

    public async Task TypingIndicator(int receiverId, bool isTyping)
    {
        var senderIdStr = Context.User?.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (senderIdStr == null) return;

        var senderId = int.Parse(senderIdStr);

        // ✅ Send typing indicator to receiver
        await Clients.Group($"user_{receiverId}").SendAsync("UserTyping", new
        {
            UserId = senderId,
            IsTyping = isTyping
        });
    }

    public override async Task OnDisconnectedAsync(Exception? exception)
    {
        var userId = Context.User?.FindFirst(ClaimTypes.NameIdentifier)?.Value;

        if (userId != null)
        {
            await Groups.RemoveFromGroupAsync(Context.ConnectionId, $"user_{userId}");

            _logger.LogInformation("User {UserId} disconnected from chat hub", userId);
        }

        await base.OnDisconnectedAsync(exception);
    }
}

// ✅ Register in Program.cs
builder.Services.AddSignalR(options =>
{
    options.EnableDetailedErrors = true;
    options.KeepAliveInterval = TimeSpan.FromSeconds(15);
    options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
});

app.MapHub<ChatHub>("/hubs/chat");
```

**Review Questions:**
- ✅ Is authentication enforced on SignalR hub?
- ✅ Are user groups managed properly (connect/disconnect)?
- ✅ Are messages delivered to correct recipients?
- ✅ Is error handling implemented?
- ✅ Are typing indicators working?
- ✅ Are read receipts implemented?

---

#### ✅ Background Jobs (Hangfire)

**GOOD Example:**
```csharp
public class StoryExpiryJob
{
    private readonly IStoryRepository _storyRepository;
    private readonly IS3Service _s3Service;
    private readonly ILogger<StoryExpiryJob> _logger;

    public StoryExpiryJob(
        IStoryRepository storyRepository,
        IS3Service s3Service,
        ILogger<StoryExpiryJob> logger)
    {
        _storyRepository = storyRepository;
        _s3Service = s3Service;
        _logger = logger;
    }

    [AutomaticRetry(Attempts = 3)]
    public async Task Execute()
    {
        _logger.LogInformation("Starting story expiry job");

        try
        {
            // ✅ Get all expired stories (older than 24 hours)
            var expiryTime = DateTime.UtcNow.AddHours(-24);
            var expiredStories = await _storyRepository.GetExpiredStories(expiryTime);

            _logger.LogInformation("Found {Count} expired stories", expiredStories.Count);

            foreach (var story in expiredStories)
            {
                try
                {
                    // ✅ Delete from S3
                    await _s3Service.DeleteFileAsync(story.MediaUrl);

                    // ✅ Delete from database
                    await _storyRepository.DeleteAsync(story.Id);

                    _logger.LogDebug("Deleted expired story {StoryId}", story.Id);
                }
                catch (Exception ex)
                {
                    _logger.LogError(ex, "Error deleting story {StoryId}", story.Id);
                    // Continue with other stories
                }
            }

            _logger.LogInformation("Story expiry job completed successfully");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Story expiry job failed");
            throw; // Re-throw for Hangfire retry
        }
    }
}

// ✅ Register in Program.cs
builder.Services.AddHangfire(config =>
{
    config.UseStorage(new MySqlStorage(
        builder.Configuration.GetConnectionString("MySQL"),
        new MySqlStorageOptions
        {
            TablesPrefix = "hangfire_"
        }
    ));
});

builder.Services.AddHangfireServer();

// ✅ Schedule recurring jobs
var recurringJobManager = app.Services.GetRequiredService<IRecurringJobManager>();

recurringJobManager.AddOrUpdate<StoryExpiryJob>(
    "story-expiry",
    job => job.Execute(),
    Cron.Hourly
);

recurringJobManager.AddOrUpdate<ChatCleanupJob>(
    "chat-cleanup",
    job => job.Execute(),
    Cron.Daily
);

recurringJobManager.AddOrUpdate<DailyLikeResetJob>(
    "daily-like-reset",
    job => job.Execute(),
    "0 0 * * *" // Midnight IST
);
```

**Review Questions:**
- ✅ Are background jobs registered correctly?
- ✅ Is error handling and retry logic implemented?
- ✅ Are jobs scheduled at correct intervals?
- ✅ Is job execution logged?
- ✅ Are jobs idempotent (safe to run multiple times)?

---

#### ✅ Security Implementation

**1. JWT Authentication**
```csharp
public class AuthService : IAuthService
{
    private readonly IUserRepository _userRepository;
    private readonly IConfiguration _configuration;
    private readonly IPasswordHasher _passwordHasher;

    public async Task<AuthResultDto> Login(string email, string password)
    {
        // ✅ Get user by email
        var user = await _userRepository.GetByEmailAsync(email);

        if (user == null)
            return new AuthResultDto { Success = false, Message = "Invalid credentials" };

        // ✅ Verify password
        var isPasswordValid = _passwordHasher.VerifyPassword(password, user.PasswordHash);

        if (!isPasswordValid)
            return new AuthResultDto { Success = false, Message = "Invalid credentials" };

        // ✅ Generate JWT token
        var token = GenerateJwtToken(user);
        var refreshToken = GenerateRefreshToken();

        // ✅ Save refresh token
        user.RefreshToken = refreshToken;
        user.RefreshTokenExpiry = DateTime.UtcNow.AddDays(30);
        await _userRepository.UpdateAsync(user);

        return new AuthResultDto
        {
            Success = true,
            Token = token,
            RefreshToken = refreshToken,
            User = MapToDto(user)
        };
    }

    private string GenerateJwtToken(User user)
    {
        var securityKey = new SymmetricSecurityKey(
            Encoding.UTF8.GetBytes(_configuration["Jwt:Secret"])
        );

        var credentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

        var claims = new[]
        {
            new Claim(ClaimTypes.NameIdentifier, user.Id.ToString()),
            new Claim(ClaimTypes.Email, user.Email),
            new Claim(ClaimTypes.Name, user.Name),
            new Claim("IsPremium", user.IsPremium.ToString()),
            new Claim("IsVerified", user.IsVerified.ToString())
        };

        var token = new JwtSecurityToken(
            issuer: _configuration["Jwt:Issuer"],
            audience: _configuration["Jwt:Audience"],
            claims: claims,
            expires: DateTime.UtcNow.AddHours(24),
            signingCredentials: credentials
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }

    private string GenerateRefreshToken()
    {
        var randomBytes = new byte[64];
        using var rng = RandomNumberGenerator.Create();
        rng.GetBytes(randomBytes);
        return Convert.ToBase64String(randomBytes);
    }
}

// ✅ Register in Program.cs
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(
                Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Secret"])
            ),
            ClockSkew = TimeSpan.Zero
        };

        // ✅ Configure for SignalR
        options.Events = new JwtBearerEvents
        {
            OnMessageReceived = context =>
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;

                if (!string.IsNullOrEmpty(accessToken) && path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }

                return Task.CompletedTask;
            }
        };
    });
```

**2. Rate Limiting**
```csharp
public class RateLimitingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly IRedisCacheService _cache;
    private readonly ILogger<RateLimitingMiddleware> _logger;

    public RateLimitingMiddleware(
        RequestDelegate next,
        IRedisCacheService cache,
        ILogger<RateLimitingMiddleware> logger)
    {
        _next = next;
        _cache = cache;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var endpoint = context.GetEndpoint();
        var rateLimitAttribute = endpoint?.Metadata.GetMetadata<RateLimitAttribute>();

        if (rateLimitAttribute != null)
        {
            var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
            var ipAddress = context.Connection.RemoteIpAddress?.ToString();

            var key = $"rate_limit:{endpoint.DisplayName}:{userId ?? ipAddress}";

            var currentCount = await _cache.GetAsync<int>(key);

            if (currentCount >= rateLimitAttribute.MaxRequests)
            {
                _logger.LogWarning("Rate limit exceeded for {Key}", key);

                context.Response.StatusCode = StatusCodes.Status429TooManyRequests;
                await context.Response.WriteAsJsonAsync(new
                {
                    error = "Too many requests. Please try again later."
                });
                return;
            }

            await _cache.IncrementAsync(key);
            await _cache.ExpireAsync(key, TimeSpan.FromMinutes(rateLimitAttribute.WindowMinutes));
        }

        await _next(context);
    }
}

// ✅ Custom attribute
[AttributeUsage(AttributeTargets.Method)]
public class RateLimitAttribute : Attribute
{
    public int MaxRequests { get; set; }
    public int WindowMinutes { get; set; }

    public RateLimitAttribute(int maxRequests = 100, int windowMinutes = 1)
    {
        MaxRequests = maxRequests;
        WindowMinutes = windowMinutes;
    }
}

// ✅ Usage
[HttpPost("swipe")]
[Authorize]
[RateLimit(maxRequests: 50, windowMinutes: 1)] // Max 50 swipes per minute
public async Task<IActionResult> Swipe([FromBody] SwipeRequestDto request)
{
    // Implementation...
}
```

**Review Questions:**
- ✅ Is JWT secret stored in configuration (not hardcoded)?
- ✅ Are passwords hashed (bcrypt/Argon2)?
- ✅ Is HTTPS enforced?
- ✅ Is rate limiting implemented on all endpoints?
- ✅ Are SQL queries parameterized (prevent injection)?
- ✅ Is input validation implemented?

---

### 4.3 Common Backend Issues to Watch For

#### ❌ RED FLAGS

1. **No Input Validation**
```csharp
// ❌ BAD: No validation
[HttpPost]
public async Task<IActionResult> CreateProfile([FromBody] ProfileDto profile)
{
    await _service.CreateProfile(profile);
    return Ok();
}

// ✅ GOOD: Proper validation
[HttpPost]
public async Task<IActionResult> CreateProfile([FromBody] ProfileDto profile)
{
    if (!ModelState.IsValid)
        return BadRequest(ModelState);

    if (profile.Age < 18)
        return BadRequest("Must be 18 or older");

    if (profile.Photos.Count > 6)
        return BadRequest("Maximum 6 photos allowed");

    await _service.CreateProfile(profile);
    return Ok();
}
```

2. **N+1 Query Problem**
```csharp
// ❌ BAD: N+1 queries
public async Task<List<MatchDto>> GetMatches(int userId)
{
    var matches = await _context.Matches
        .Where(m => m.User1Id == userId || m.User2Id == userId)
        .ToListAsync();

    foreach (var match in matches)
    {
        // ❌ Separate query for each match (N+1)
        match.User = await _context.Users.FindAsync(match.User2Id);
    }

    return matches;
}

// ✅ GOOD: Eager loading
public async Task<List<MatchDto>> GetMatches(int userId)
{
    var matches = await _context.Matches
        .Include(m => m.User1)  // ✅ Load related data in one query
        .Include(m => m.User2)
        .Where(m => m.User1Id == userId || m.User2Id == userId)
        .ToListAsync();

    return matches;
}
```

3. **Exposing Sensitive Data**
```csharp
// ❌ BAD: Returning entity with password
[HttpGet("profile")]
public async Task<User> GetProfile()
{
    var userId = GetUserId();
    return await _context.Users.FindAsync(userId); // ❌ Includes PasswordHash!
}

// ✅ GOOD: Use DTOs
[HttpGet("profile")]
public async Task<UserProfileDto> GetProfile()
{
    var userId = GetUserId();
    var user = await _context.Users.FindAsync(userId);

    return new UserProfileDto
    {
        Id = user.Id,
        Name = user.Name,
        Email = user.Email,
        // ✅ No PasswordHash exposed
    };
}
```

4. **No Transaction Handling**
```csharp
// ❌ BAD: No transaction for multiple operations
public async Task CreateMatch(int user1Id, int user2Id)
{
    // ❌ If second insert fails, first one remains (data inconsistency)
    await _context.Matches.AddAsync(new Match { User1Id = user1Id, User2Id = user2Id });
    await _context.SaveChangesAsync();

    await _context.Notifications.AddAsync(new Notification { UserId = user1Id });
    await _context.SaveChangesAsync(); // ❌ Might fail here
}

// ✅ GOOD: Use transaction
public async Task CreateMatch(int user1Id, int user2Id)
{
    using var transaction = await _context.Database.BeginTransactionAsync();

    try
    {
        await _context.Matches.AddAsync(new Match { User1Id = user1Id, User2Id = user2Id });
        await _context.SaveChangesAsync();

        await _context.Notifications.AddAsync(new Notification { UserId = user1Id });
        await _context.SaveChangesAsync();

        await transaction.CommitAsync(); // ✅ All or nothing
    }
    catch
    {
        await transaction.RollbackAsync();
        throw;
    }
}
```

---

### 4.4 How to Test Backend Code (Without Being a Developer)

#### Using Postman/Swagger

**Test Authentication:**
```
1. Open Swagger at: http://localhost:5000/swagger
2. Find POST /api/v1/auth/register
3. Click "Try it out"
4. Enter test data:
   {
     "email": "test@example.com",
     "password": "Test@123",
     "name": "Test User"
   }
5. Click "Execute"
6. Response should be 200 OK with token
7. Copy the token value
8. Click "Authorize" button at top
9. Paste token in format: Bearer <token>
10. Now all authenticated endpoints should work
```

**Test Discovery API:**
```
1. Go to GET /api/v1/discovery/profiles
2. Should return nearby user profiles
3. Check response:
   - Should have array of users
   - Each user should have: id, name, age, photos, distance
   - No user passwords or sensitive data
```

**Test Swipe Limit:**
```
1. Login as free user
2. Call POST /api/v1/discovery/swipe 25 times with isLike=true
3. On 26th call, should return error: "Daily limit reached"
4. Login as premium user
5. Should be able to swipe unlimited times
```

**Test Match Detection:**
```
1. User A swipes right on User B
2. Response: { "isMatch": false }
3. User B swipes right on User A
4. Response: { "isMatch": true }
5. Both users should receive push notification
6. Match should appear in GET /api/v1/discovery/matches
```

---

## 5. Database Developer Guide & Review

### 5.1 Database Schema Review

**Critical Tables:**
```sql
-- Users table
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    age INT NOT NULL CHECK (age >= 18),
    latitude DOUBLE NOT NULL,
    longitude DOUBLE NOT NULL,
    profession VARCHAR(100),
    bio TEXT,
    languages VARCHAR(255), -- Comma-separated
    interests VARCHAR(500), -- Comma-separated tags
    photos JSON, -- Array of photo URLs
    gender ENUM('male', 'female', 'other') NOT NULL,
    zodiac VARCHAR(20),
    is_verified BOOLEAN DEFAULT FALSE,
    is_premium BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_active TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    -- ✅ Indexes for performance
    INDEX idx_location (latitude, longitude),
    INDEX idx_verified_premium (is_verified, is_premium),
    INDEX idx_last_active (last_active),
    INDEX idx_gender_age (gender, age)
) ENGINE=InnoDB;

-- Likes table
CREATE TABLE likes (
    id INT PRIMARY KEY AUTO_INCREMENT,
    from_user_id INT NOT NULL,
    to_user_id INT NOT NULL,
    liked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (from_user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (to_user_id) REFERENCES users(id) ON DELETE CASCADE,

    -- ✅ Prevent duplicate likes
    UNIQUE KEY unique_like (from_user_id, to_user_id),

    -- ✅ Indexes for queries
    INDEX idx_from_user (from_user_id, liked_at),
    INDEX idx_to_user (to_user_id, liked_at)
) ENGINE=InnoDB;

-- Matches table
CREATE TABLE matches (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user1_id INT NOT NULL,
    user2_id INT NOT NULL,
    matched_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (user1_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (user2_id) REFERENCES users(id) ON DELETE CASCADE,

    -- ✅ Prevent duplicate matches
    UNIQUE KEY unique_match (user1_id, user2_id),

    -- ✅ Indexes for queries
    INDEX idx_user1_matches (user1_id, matched_at),
    INDEX idx_user2_matches (user2_id, matched_at)
) ENGINE=InnoDB;

-- Messages table
CREATE TABLE messages (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    sender_id INT NOT NULL,
    receiver_id INT NOT NULL,
    content TEXT NOT NULL,
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_read BOOLEAN DEFAULT FALSE,
    read_at TIMESTAMP NULL,

    FOREIGN KEY (sender_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (receiver_id) REFERENCES users(id) ON DELETE CASCADE,

    -- ✅ Indexes for chat queries
    INDEX idx_conversation (sender_id, receiver_id, sent_at),
    INDEX idx_receiver_unread (receiver_id, is_read, sent_at)
) ENGINE=InnoDB
-- ✅ Partition by date for performance
PARTITION BY RANGE (YEAR(sent_at) * 100 + MONTH(sent_at)) (
    PARTITION p202401 VALUES LESS THAN (202402),
    PARTITION p202402 VALUES LESS THAN (202403),
    -- Add partitions monthly
);

-- Stories table
CREATE TABLE stories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    media_type ENUM('photo', 'video') NOT NULL,
    media_url VARCHAR(500) NOT NULL,
    posted_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,

    -- ✅ Index for expiry job
    INDEX idx_expires (expires_at),
    INDEX idx_user_posted (user_id, posted_at)
) ENGINE=InnoDB;

-- Story replies
CREATE TABLE story_replies (
    id INT PRIMARY KEY AUTO_INCREMENT,
    story_id INT NOT NULL,
    replier_id INT NOT NULL,
    content TEXT NOT NULL,
    replied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (story_id) REFERENCES stories(id) ON DELETE CASCADE,
    FOREIGN KEY (replier_id) REFERENCES users(id) ON DELETE CASCADE,

    INDEX idx_story (story_id, replied_at)
) ENGINE=InnoDB;

-- Subscriptions table
CREATE TABLE subscriptions (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    tier ENUM('basic_premium', 'gold', 'platinum') NOT NULL,
    start_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    end_date TIMESTAMP NOT NULL,
    payment_method VARCHAR(50),
    payment_id VARCHAR(255),

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,

    INDEX idx_user_active (user_id, end_date)
) ENGINE=InnoDB;

-- User reports
CREATE TABLE user_reports (
    id INT PRIMARY KEY AUTO_INCREMENT,
    reporter_id INT NOT NULL,
    reported_user_id INT NOT NULL,
    reason VARCHAR(255) NOT NULL,
    description TEXT,
    status ENUM('pending', 'reviewed', 'action_taken') DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    reviewed_at TIMESTAMP NULL,
    reviewer_id INT NULL,

    FOREIGN KEY (reporter_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (reported_user_id) REFERENCES users(id) ON DELETE CASCADE,

    INDEX idx_status (status, created_at),
    INDEX idx_reported_user (reported_user_id)
) ENGINE=InnoDB;

-- User blocks
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

-- Notifications
CREATE TABLE notifications (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    type ENUM('like', 'match', 'message', 'story_reply', 'system') NOT NULL,
    reference_id INT,
    title VARCHAR(100) NOT NULL,
    body TEXT NOT NULL,
    is_read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,

    INDEX idx_user_unread (user_id, is_read, created_at)
) ENGINE=InnoDB;
```

**Review Questions:**
- ✅ Are all foreign keys defined with ON DELETE CASCADE?
- ✅ Are indexes created on commonly queried columns?
- ✅ Are UNIQUE constraints applied where needed?
- ✅ Is data type appropriate (INT vs BIGINT for messages)?
- ✅ Are partitions set up for large tables (messages)?
- ✅ Are CHECK constraints used for data validation?

---

### 5.2 Critical Queries to Review

**1. Proximity Search Query**
```sql
-- ✅ GOOD: Optimized with spatial index
SELECT
    u.id,
    u.name,
    u.age,
    u.photos,
    u.bio,
    u.is_verified,
    ST_Distance_Sphere(
        POINT(u.longitude, u.latitude),
        POINT(@userLongitude, @userLatitude)
    ) / 1000 AS distance_km
FROM users u
LEFT JOIN likes l ON l.from_user_id = @userId AND l.to_user_id = u.id
LEFT JOIN user_blocks b1 ON b1.blocker_id = @userId AND b1.blocked_id = u.id
LEFT JOIN user_blocks b2 ON b2.blocker_id = u.id AND b2.blocked_id = @userId
WHERE u.id != @userId
  AND l.id IS NULL          -- Not already liked
  AND b1.id IS NULL         -- Not blocked by me
  AND b2.id IS NULL         -- Hasn't blocked me
  AND u.is_verified = 1     -- Only verified users
HAVING distance_km <= @radiusKm
ORDER BY
    u.is_verified DESC,     -- Verified users first
    distance_km ASC,        -- Closest first
    u.last_active DESC      -- Recently active first
LIMIT 50;

-- ❌ BAD: No indexes, no spatial function
SELECT * FROM users
WHERE latitude BETWEEN @lat - 1 AND @lat + 1
  AND longitude BETWEEN @lon - 1 AND @lon + 1;
```

**Review: Does query use ST_Distance_Sphere and have proper indexes?**

---

**2. Match Detection Query**
```sql
-- Check if mutual like exists (for match detection)
SELECT 1
FROM likes l1
INNER JOIN likes l2
    ON l1.from_user_id = l2.to_user_id
    AND l1.to_user_id = l2.from_user_id
WHERE l1.from_user_id = @user1Id
  AND l1.to_user_id = @user2Id
LIMIT 1;
```

**Review: Is this query fast? (Should use indexes on from_user_id and to_user_id)**

---

**3. Chat History Query**
```sql
-- Get conversation between two users
SELECT
    m.id,
    m.sender_id,
    m.receiver_id,
    m.content,
    m.sent_at,
    m.is_read,
    m.read_at,
    u.name AS sender_name,
    u.photos AS sender_photo
FROM messages m
INNER JOIN users u ON u.id = m.sender_id
WHERE (m.sender_id = @user1Id AND m.receiver_id = @user2Id)
   OR (m.sender_id = @user2Id AND m.receiver_id = @user1Id)
ORDER BY m.sent_at DESC
LIMIT 50 OFFSET @offset;

-- ✅ Index needed: idx_conversation (sender_id, receiver_id, sent_at)
```

---

### 5.3 Database Performance Optimization

#### ✅ Indexing Strategy
```sql
-- Analyze slow queries
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;

-- Explain query execution plan
EXPLAIN SELECT * FROM users WHERE ...;

-- If "type" column shows "ALL" (table scan), add index!
-- If "rows" is very high, optimize query

-- Add indexes for common queries
ALTER TABLE users ADD INDEX idx_location_verified (latitude, longitude, is_verified);
ALTER TABLE messages ADD INDEX idx_unread (receiver_id, is_read, sent_at);
ALTER TABLE likes ADD INDEX idx_daily_count (from_user_id, liked_at);
```

#### ✅ Query Optimization
```sql
-- ❌ BAD: COUNT(*) on large table
SELECT COUNT(*) FROM messages WHERE receiver_id = @userId;

-- ✅ GOOD: Use covering index
SELECT COUNT(id) FROM messages
WHERE receiver_id = @userId
  AND is_read = 0;
-- Ensure index: idx_receiver_unread (receiver_id, is_read)

-- ❌ BAD: OR conditions (slow)
SELECT * FROM users
WHERE email = @search OR name LIKE CONCAT('%', @search, '%');

-- ✅ GOOD: Use UNION
SELECT * FROM users WHERE email = @search
UNION
SELECT * FROM users WHERE name LIKE CONCAT('%', @search, '%');
```

---

### 5.4 Data Migration & Seeding

**Seed Data for Testing:**
```sql
-- Insert test users
INSERT INTO users (email, password_hash, name, age, latitude, longitude, gender, profession, bio, interests, photos, is_verified)
VALUES
-- Mumbai users
('test1@example.com', '$2a$12$...', 'Priya Sharma', 25, 19.0760, 72.8777, 'female', 'Software Engineer', 'Love traveling and photography', 'travel,photography,music', '["https://example.com/photo1.jpg"]', 1),
('test2@example.com', '$2a$12$...', 'Rahul Mehta', 28, 19.0760, 72.8777, 'male', 'Product Manager', 'Fitness enthusiast and foodie', 'fitness,food,movies', '["https://example.com/photo2.jpg"]', 1),

-- Delhi users
('test3@example.com', '$2a$12$...', 'Sneha Gupta', 26, 28.7041, 77.1025, 'female', 'Designer', 'Art lover and coffee addict', 'art,design,coffee', '["https://example.com/photo3.jpg"]', 1),
('test4@example.com', '$2a$12$...', 'Arjun Singh', 30, 28.7041, 77.1025, 'male', 'Entrepreneur', 'Building the future', 'startups,tech,travel', '["https://example.com/photo4.jpg"]', 1);

-- Create some test matches
INSERT INTO likes (from_user_id, to_user_id) VALUES
(1, 2), -- Priya likes Rahul
(2, 1), -- Rahul likes Priya (this creates a match)
(3, 4); -- Sneha likes Arjun

INSERT INTO matches (user1_id, user2_id) VALUES
(1, 2); -- Priya and Rahul matched

-- Create some test messages
INSERT INTO messages (sender_id, receiver_id, content) VALUES
(1, 2, 'Hey! How are you?'),
(2, 1, 'Hi Priya! I''m good, thanks!'),
(1, 2, 'Want to grab coffee sometime?');
```

---

### 5.5 Database Maintenance

**Regular Tasks:**
```sql
-- 1. Analyze and optimize tables (run weekly)
ANALYZE TABLE users, likes, matches, messages;
OPTIMIZE TABLE messages; -- Defragment

-- 2. Check index usage
SELECT
    TABLE_NAME,
    INDEX_NAME,
    SEQ_IN_INDEX,
    COLUMN_NAME,
    CARDINALITY
FROM information_schema.STATISTICS
WHERE TABLE_SCHEMA = 'initiate_db'
ORDER BY TABLE_NAME, INDEX_NAME, SEQ_IN_INDEX;

-- 3. Find unused indexes (careful before dropping!)
SELECT
    object_schema,
    object_name,
    index_name
FROM performance_schema.table_io_waits_summary_by_index_usage
WHERE index_name IS NOT NULL
  AND count_star = 0
  AND object_schema = 'initiate_db';

-- 4. Monitor table sizes
SELECT
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb
FROM information_schema.TABLES
WHERE table_schema = 'initiate_db'
ORDER BY (data_length + index_length) DESC;

-- 5. Clean up old data (run monthly)
-- Delete messages older than 6 months (free users)
DELETE m FROM messages m
INNER JOIN users u1 ON u1.id = m.sender_id
INNER JOIN users u2 ON u2.id = m.receiver_id
WHERE u1.is_premium = 0
  AND u2.is_premium = 0
  AND m.sent_at < DATE_SUB(NOW(), INTERVAL 6 MONTH);

-- Delete expired stories
DELETE FROM stories WHERE expires_at < NOW();
```

---

## 6. Code Review Checklist

### 6.1 General Code Quality

**For Both Flutter & Backend:**
- [ ] Code is readable and well-organized
- [ ] Variable names are meaningful (not x, y, temp)
- [ ] No commented-out code (remove it!)
- [ ] No TODO comments (fix them or create tasks)
- [ ] No console.log or print statements (use proper logging)
- [ ] No hardcoded values (use constants/config)
- [ ] Error handling is implemented
- [ ] Code is DRY (Don't Repeat Yourself)
- [ ] Functions are small (< 50 lines)
- [ ] Files are small (< 500 lines)

### 6.2 Security Checklist
- [ ] No passwords or API keys in code
- [ ] All user input is validated
- [ ] SQL queries are parameterized
- [ ] Authentication is required on protected endpoints
- [ ] HTTPS is enforced
- [ ] Rate limiting is implemented
- [ ] Sensitive data is not logged
- [ ] File uploads are validated (type, size)

### 6.3 Performance Checklist
- [ ] Database queries are optimized (no N+1)
- [ ] Indexes are created on queried columns
- [ ] Caching is used for expensive operations
- [ ] Images are compressed and cached
- [ ] API responses are paginated
- [ ] Heavy operations run in background
- [ ] No blocking operations on UI thread

### 6.4 Testing Checklist
- [ ] Unit tests cover business logic
- [ ] Integration tests cover API endpoints
- [ ] Edge cases are tested (empty data, errors)
- [ ] All tests pass
- [ ] Test coverage > 70%

---

## 7. Common Issues & Red Flags

### 7.1 Critical Red Flags (Fix Immediately)

**🚨 Security Issues:**
- Passwords visible in plain text
- API keys committed to git
- No authentication on endpoints
- SQL injection vulnerability

**🚨 Data Loss Risks:**
- No transaction handling for critical operations
- No database backups
- Cascade deletes not configured

**🚨 Performance Issues:**
- Database table scans (no indexes)
- API returning 1000+ records without pagination
- Images not compressed (> 5MB per photo)
- Memory leaks (controllers not disposed)

### 7.2 Medium Priority Issues

**⚠️ Code Quality:**
- Business logic in UI/Controller
- Duplicate code across files
- 1000+ line files
- No error handling

**⚠️ User Experience:**
- No loading indicators
- No error messages
- Slow API responses (> 3 seconds)

### 7.3 Low Priority Issues

**ℹ️ Improvements:**
- Missing code comments
- Inconsistent naming conventions
- Could use more efficient algorithm

---

## 8. Quality Standards

### 8.1 Definition of "Done"

A feature is considered complete when:
- ✅ Code is written and follows architecture
- ✅ Code is reviewed and approved
- ✅ Unit tests are written and passing
- ✅ Integration tests are passing
- ✅ Feature works on real device/environment
- ✅ Performance is acceptable (< 2s for API calls)
- ✅ No critical or high-priority bugs
- ✅ Documentation is updated

### 8.2 API Response Time Standards
- Authentication: < 500ms
- Profile load: < 1s
- Discovery feed: < 2s
- Swipe action: < 500ms
- Send message: < 300ms
- Image upload: < 5s

### 8.3 Mobile App Performance Standards
- App launch: < 3s
- Screen transitions: < 300ms
- Scroll FPS: 60 FPS (smooth)
- Memory usage: < 200MB
- Battery drain: < 5% per hour

---

## 9. Development Workflow

### 9.1 Sprint Structure (2-Week Sprints)

**Week 1:**
- Monday: Sprint planning, assign tasks
- Tuesday-Thursday: Development
- Friday: Code review, testing

**Week 2:**
- Monday-Wednesday: Bug fixes, testing
- Thursday: Demo to stakeholders
- Friday: Sprint retrospective, plan next sprint

### 9.2 Git Workflow

**Branch Strategy:**
```
main (production)
  └── develop (staging)
       ├── feature/swipe-interface
       ├── feature/chat-implementation
       └── bugfix/profile-photo-upload
```

**Commit Message Format:**
```
feat: Add swipe animation to discovery screen
fix: Fix crash when uploading profile photo
refactor: Optimize nearby users query
test: Add unit tests for matching service
docs: Update API documentation for story endpoints
```

### 9.3 Code Review Process

1. **Developer creates Pull Request**
   - Title: Clear description of changes
   - Description: What, why, how
   - Screenshots (for UI changes)
   - Test results

2. **Reviewer checks:**
   - Code follows architecture
   - Tests are included
   - No security issues
   - Performance is acceptable

3. **Approval or Request Changes**
   - Approve: Merge to develop
   - Request changes: Developer fixes and updates PR

4. **Testing on Staging**
   - QA tests on staging environment
   - If all good, merge to main

---

## 10. Testing Requirements

### 10.1 Testing Pyramid

```
        /\
       /  \
      / UI \           10% - End-to-end tests
     /______\
    /        \
   /Integration\      30% - Integration tests
  /____________\
 /              \
/  Unit Tests    \    60% - Unit tests
/__________________\
```

### 10.2 Flutter Testing

**Unit Tests:**
```dart
// Test business logic
void main() {
  group('MatchingService', () {
    late MatchingService service;
    late MockRepository mockRepo;

    setUp(() {
      mockRepo = MockRepository();
      service = MatchingService(mockRepo);
    });

    test('should return match when both users like each other', () async {
      // Arrange
      when(mockRepo.getLike(1, 2)).thenAnswer((_) async => Like(...));
      when(mockRepo.getLike(2, 1)).thenAnswer((_) async => Like(...));

      // Act
      final result = await service.checkMatch(1, 2);

      // Assert
      expect(result.isMatch, true);
    });

    test('should enforce daily like limit for free users', () async {
      // Arrange
      when(mockRepo.getUser(1)).thenAnswer((_) async =>
        User(id: 1, isPremium: false));
      when(mockRepo.getDailyLikeCount(1)).thenAnswer((_) async => 25);

      // Act
      final result = await service.processSwipe(1, 2, true);

      // Assert
      expect(result.success, false);
      expect(result.message, contains('limit'));
    });
  });
}
```

**Widget Tests:**
```dart
void main() {
  testWidgets('SwipeCard displays user info correctly', (tester) async {
    // Arrange
    final user = UserProfile(
      name: 'Test User',
      age: 25,
      photoUrl: 'https://example.com/photo.jpg',
    );

    // Act
    await tester.pumpWidget(
      MaterialApp(home: SwipeCard(profile: user))
    );

    // Assert
    expect(find.text('Test User, 25'), findsOneWidget);
    expect(find.byType(CachedNetworkImage), findsOneWidget);
  });
}
```

### 10.3 Backend Testing

**Unit Tests:**
```csharp
public class MatchingServiceTests
{
    private readonly Mock<IUserRepository> _userRepoMock;
    private readonly Mock<ILikeRepository> _likeRepoMock;
    private readonly MatchingService _service;

    public MatchingServiceTests()
    {
        _userRepoMock = new Mock<IUserRepository>();
        _likeRepoMock = new Mock<ILikeRepository>();
        _service = new MatchingService(_userRepoMock.Object, _likeRepoMock.Object);
    }

    [Fact]
    public async Task ProcessSwipe_FreeUserExceedsLimit_ReturnError()
    {
        // Arrange
        var user = new User { Id = 1, IsPremium = false };
        _userRepoMock.Setup(x => x.GetByIdAsync(1)).ReturnsAsync(user);
        _likeRepoMock.Setup(x => x.GetDailyLikeCountAsync(1)).ReturnsAsync(25);

        // Act
        var result = await _service.ProcessSwipe(1, 2, true);

        // Assert
        Assert.False(result.Success);
        Assert.Contains("limit", result.Message);
    }

    [Fact]
    public async Task ProcessSwipe_MutualLike_CreatesMatch()
    {
        // Arrange
        var user = new User { Id = 1, IsPremium = true };
        _userRepoMock.Setup(x => x.GetByIdAsync(1)).ReturnsAsync(user);
        _likeRepoMock.Setup(x => x.GetLikeAsync(2, 1))
            .ReturnsAsync(new Like { FromUserId = 2, ToUserId = 1 });

        // Act
        var result = await _service.ProcessSwipe(1, 2, true);

        // Assert
        Assert.True(result.Success);
        Assert.True(result.IsMatch);
    }
}
```

**Integration Tests:**
```csharp
public class DiscoveryApiTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public DiscoveryApiTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetNearbyProfiles_WithValidAuth_ReturnsProfiles()
    {
        // Arrange
        var token = await GetAuthTokenAsync();
        _client.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", token);

        // Act
        var response = await _client.GetAsync("/api/v1/discovery/profiles?radiusKm=50");

        // Assert
        response.EnsureSuccessStatusCode();
        var content = await response.Content.ReadAsStringAsync();
        var profiles = JsonSerializer.Deserialize<List<UserProfileDto>>(content);

        Assert.NotNull(profiles);
        Assert.True(profiles.Count > 0);
        Assert.All(profiles, p => Assert.True(p.DistanceKm <= 50));
    }
}
```

---

## 11. Deployment Checklist

### 11.1 Pre-Deployment
- [ ] All tests passing
- [ ] Code reviewed and approved
- [ ] Database migrations prepared
- [ ] Environment variables configured
- [ ] API keys and secrets secured
- [ ] Performance tested (load testing)
- [ ] Security scan completed

### 11.2 Deployment Steps

**Backend Deployment (AWS):**
```bash
# 1. Build Docker image
docker build -t initiate-api:v1.0.0 .

# 2. Push to ECR
aws ecr get-login-password | docker login --username AWS --password-stdin
docker tag initiate-api:v1.0.0 <ecr-url>/initiate-api:v1.0.0
docker push <ecr-url>/initiate-api:v1.0.0

# 3. Update ECS service
aws ecs update-service --cluster initiate-cluster --service api-service --force-new-deployment

# 4. Run database migrations
dotnet ef database update --connection-string "<prod-connection>"

# 5. Verify deployment
curl https://api.initiate.com/health
```

**Mobile App Deployment:**
```bash
# Android
cd mobile
flutter build appbundle --release
# Upload to Google Play Console

# iOS
flutter build ipa --release
# Upload to App Store Connect via Xcode
```

### 11.3 Post-Deployment
- [ ] Health check endpoint returns 200
- [ ] Can login successfully
- [ ] Key features work (swipe, chat, match)
- [ ] No errors in logs
- [ ] Performance metrics look good
- [ ] Notify team of successful deployment

---

## 12. Monitoring & Alerting

### 12.1 Key Metrics to Monitor

**Application Metrics:**
- API response times (p50, p95, p99)
- Error rate (< 1%)
- Request rate (requests/minute)
- Active users
- Database connection pool usage

**Business Metrics:**
- Daily active users (DAU)
- New registrations
- Matches created
- Messages sent
- Premium conversions

**Infrastructure Metrics:**
- CPU usage (< 70%)
- Memory usage (< 80%)
- Database connections
- Redis cache hit rate (> 90%)

### 12.2 Alerts to Configure

**Critical Alerts (Page immediately):**
- API error rate > 5%
- Database down
- Redis down
- API response time > 10s

**Warning Alerts (Email):**
- API error rate > 1%
- CPU usage > 80%
- Memory usage > 90%
- Low cache hit rate < 70%

---

## 13. Troubleshooting Guide

### 13.1 Common Issues

**Issue: "API returning 401 Unauthorized"**
```
Possible causes:
1. JWT token expired
2. Invalid token
3. Token not included in header

How to fix:
- Check if token is being sent: Authorization: Bearer <token>
- Verify token expiry time
- Try logging in again to get fresh token
```

**Issue: "Swipe not working"**
```
Possible causes:
1. Daily limit reached (free users)
2. API error
3. Network connectivity

How to debug:
- Check if user is premium
- Check API response in network tab
- Verify daily like count in Redis
```

**Issue: "Messages not delivering in real-time"**
```
Possible causes:
1. SignalR connection not established
2. Receiver offline
3. SignalR hub error

How to debug:
- Check SignalR connection state
- Verify hub methods are registered
- Check server logs for SignalR errors
```

**Issue: "Nearby users not showing"**
```
Possible causes:
1. GPS permission not granted
2. Location not updated
3. No users within radius
4. Spatial index missing

How to debug:
- Check if location permission is granted
- Verify latitude/longitude are valid
- Check database spatial index exists
- Test with larger radius
```

---

## 14. Communication & Reporting

### 14.1 Daily Standup Format

**What each developer should report:**
1. What I completed yesterday
2. What I'm working on today
3. Any blockers or issues

Example:
```
Flutter Developer:
✅ Yesterday: Completed swipe animation, fixed photo upload bug
🔨 Today: Working on chat UI, implementing typing indicator
🚧 Blockers: Need API endpoint for story replies

Backend Developer:
✅ Yesterday: Implemented match detection logic, added unit tests
🔨 Today: Working on SignalR chat hub, deploying to staging
🚧 Blockers: Need AWS Rekognition API key for verification
```

### 14.2 Weekly Progress Report

```
Week 12 Progress Report (Jan 1-5, 2026)

Completed:
- ✅ Discovery swipe interface (Flutter)
- ✅ Match detection API (Backend)
- ✅ Daily like limit enforcement (Backend)
- ✅ Database indexing optimization

In Progress:
- 🔨 Chat functionality (Flutter + Backend)
- 🔨 Story feature (Backend)

Upcoming Next Week:
- 📅 Real-time messaging with SignalR
- 📅 Push notifications
- 📅 Premium subscription flow

Blockers:
- 🚧 Waiting for payment gateway integration approval
- 🚧 Need design mockups for premium upsell screen

Metrics:
- API response time: 850ms avg (target: < 1s) ✅
- Test coverage: 75% (target: 70%) ✅
- Open bugs: 3 (all low priority)
```

---

## 15. Learning Resources

### 15.1 For You (Non-Technical Lead)

**Understanding APIs:**
- Video: "What is an API?" by MuleSoft (YouTube)
- Article: "APIs for Dummies" (simple explanation)

**Understanding Databases:**
- Video: "Database Tutorial for Beginners" by freeCodeCamp
- Interactive: SQL Murder Mystery (learn SQL through a game)

**Understanding Mobile Apps:**
- Video: "How Mobile Apps Work" by Crash Course

### 15.2 For Flutter Developer

**Official Documentation:**
- Flutter.dev documentation
- Riverpod documentation
- Dio (HTTP client) documentation

**Best Practices:**
- Flutter Architecture Blueprints (book)
- Clean Architecture in Flutter (article series)

### 15.3 For Backend Developer

**Official Documentation:**
- Microsoft .NET Core documentation
- Entity Framework Core documentation
- SignalR documentation

**Best Practices:**
- Clean Architecture by Robert C. Martin
- Domain-Driven Design patterns
- RESTful API design principles

---

## 16. Quick Reference

### 16.1 Important Commands

**Flutter:**
```bash
flutter doctor              # Check setup
flutter pub get            # Install dependencies
flutter run                # Run on device/simulator
flutter test               # Run tests
flutter build apk          # Build Android APK
flutter clean              # Clean build cache
```

**Backend (.NET):**
```bash
dotnet restore             # Restore packages
dotnet build               # Build project
dotnet run                 # Run application
dotnet test                # Run tests
dotnet ef migrations add   # Create migration
dotnet ef database update  # Apply migrations
```

**Database:**
```sql
SHOW PROCESSLIST;          -- See running queries
EXPLAIN <query>;           -- Analyze query performance
SHOW INDEX FROM <table>;   -- See table indexes
SHOW TABLE STATUS;         -- See table sizes
```

**Docker:**
```bash
docker ps                  # List running containers
docker logs <container>    # View logs
docker exec -it <container> bash  # Enter container
docker-compose up -d       # Start all services
docker-compose down        # Stop all services
```

### 16.2 Important File Locations

**Flutter:**
```
mobile/lib/core/constants/api_constants.dart  - API endpoints
mobile/lib/config/routes/app_routes.dart      - Screen navigation
mobile/lib/main.dart                          - App entry point
mobile/pubspec.yaml                           - Dependencies
```

**Backend:**
```
backend/src/Initiate.API/appsettings.json     - Configuration
backend/src/Initiate.API/Program.cs           - App startup
backend/src/Initiate.Application/Services/    - Business logic
backend/src/Initiate.Infrastructure/Data/     - Database code
```

**Database:**
```
database/schema.sql                           - Database schema
database/migrations/                          - Migration scripts
database/seeds/test_data.sql                  - Test data
```

---

## 17. Glossary

**API** - Application Programming Interface; how frontend talks to backend

**Authentication** - Verifying user identity (login)

**Authorization** - Checking if user has permission to do something

**DTO** - Data Transfer Object; simplified data for API responses

**Entity** - Database table represented as a class

**JWT** - JSON Web Token; secure way to transmit user identity

**Repository** - Class that handles database operations

**Service** - Class that contains business logic

**Controller** - Class that handles HTTP requests

**Hub** - SignalR class for real-time communication

**Migration** - Script to update database schema

**Seed** - Initial data for database

**Cache** - Temporary storage for fast access (Redis)

**Spatial Query** - Database query using GPS coordinates

**N+1 Problem** - Performance issue from too many database queries

**Dependency Injection** - Design pattern for providing dependencies

**Clean Architecture** - Layered architecture pattern

**CRUD** - Create, Read, Update, Delete operations

---

## Conclusion

This guide should help you effectively manage your developers and review their code, even without deep technical knowledge. The key is to:

1. **Understand the architecture** - Know how pieces fit together
2. **Look for patterns** - Good code follows consistent patterns
3. **Check the checklist** - Use the review checklists provided
4. **Test thoroughly** - If it works as expected, code is probably good
5. **Monitor metrics** - Performance numbers don't lie
6. **Trust but verify** - Ask developers to explain their code

Remember: You don't need to write code to review it. You need to understand:
- Does it work?
- Is it secure?
- Is it fast enough?
- Can it be maintained?

Use this document as your reference bible. Good luck with your project!

---

**Document Version:** 1.0
**Last Updated:** January 4, 2026
**Next Review:** February 4, 2026
