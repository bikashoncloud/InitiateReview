# 🚀 INITIATE APP - MVP LAUNCH CHECKLIST
## Print This & Check Off Daily!

**Launch Target:** February 20, 2026
**Days Remaining:** _____

---

## 📱 MOBILE APP (Flutter) - CRITICAL FEATURES

### 🔐 Authentication (Week 1)
- [ ] Splash screen
- [ ] Login screen
- [ ] Registration screen
- [ ] OTP verification screen
- [ ] Email validation
- [ ] Password validation (8+ chars, 1 number, 1 special)
- [ ] Token storage (SharedPreferences)
- [ ] Auto-login if token valid
- [ ] Logout functionality
- [ ] "Forgot Password" flow

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 👤 Profile Management (Week 1 & 4)
- [ ] Profile creation wizard (3 steps)
  - [ ] Step 1: Basic info (name, age, gender, profession, bio)
  - [ ] Step 2: Photos (1-6 images)
  - [ ] Step 3: Interests & languages
- [ ] Image picker from gallery
- [ ] Image compression (<500KB)
- [ ] Photo upload to server
- [ ] Profile preview before submit
- [ ] View own profile
- [ ] Edit profile
- [ ] Update profile info
- [ ] Add/remove photos
- [ ] Reorder photos (drag & drop)
- [ ] View other user's profile
- [ ] Profile photo gallery viewer
- [ ] Age validation (18+ only)
- [ ] Profile completeness indicator

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### ✔️ Verification (Week 4)
- [ ] Selfie verification screen
- [ ] Camera integration
- [ ] Face detection guide overlay
- [ ] Capture selfie
- [ ] Upload verification photo
- [ ] Show verification status (Pending/Verified/Rejected)
- [ ] Verified badge display
- [ ] Re-submit if rejected

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🔍 Discovery & Swipe (Week 2)
- [ ] Home screen with bottom navigation
- [ ] Discovery feed screen
- [ ] Swipe card widget
- [ ] Left swipe animation (pass)
- [ ] Right swipe animation (like)
- [ ] Profile card display
  - [ ] Main photo
  - [ ] Name and age
  - [ ] Distance from user
  - [ ] Verified badge
  - [ ] Brief bio
  - [ ] Interests tags
- [ ] Multi-photo carousel (swipe up for more photos)
- [ ] Profile detail view (tap to expand)
- [ ] Daily like counter display (X/25)
- [ ] Daily limit reached modal
- [ ] "Upgrade to Premium" button at limit
- [ ] No more profiles screen
- [ ] Pull to refresh
- [ ] Loading shimmer effect
- [ ] Error handling (no internet, API error)
- [ ] Location permission request
- [ ] Settings to change radius (25km, 50km, 100km)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💕 Matching (Week 2)
- [ ] Match detection on mutual like
- [ ] Match celebration animation/dialog
- [ ] "It's a Match!" screen
- [ ] Option to send first message or keep swiping
- [ ] Matches list screen
- [ ] Match profile cards
- [ ] Last message preview in list
- [ ] Unread indicator
- [ ] "Who Liked Me" screen
- [ ] Blurred profiles for free users
- [ ] Clear profiles for premium users
- [ ] Empty state (no matches yet)
- [ ] Unmatch option (with confirmation)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💬 Chat (Week 3)
- [ ] Conversations list screen
- [ ] Chat detail screen
- [ ] Message bubble (sent)
- [ ] Message bubble (received)
- [ ] Timestamp display
- [ ] "Today" / "Yesterday" / date formatting
- [ ] Text input field
- [ ] Send button
- [ ] Message sending (with loading)
- [ ] Message receiving (polling every 3s)
- [ ] Scroll to bottom on new message
- [ ] Auto-scroll as user types
- [ ] Read receipts (single/double checkmark)
- [ ] "Seen" status
- [ ] Typing indicator (for when you add real-time)
- [ ] Female-first messaging modal
  - [ ] "Waiting for her to message first" screen
  - [ ] Override for premium users
- [ ] Empty chat state
- [ ] Long message handling (multi-line)
- [ ] Timestamp grouping (show time every few messages)
- [ ] Report user option
- [ ] Block user option
- [ ] Delete conversation option
- [ ] Unread badge count

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💎 Premium Features (Week 5)
- [ ] Subscription plans screen
- [ ] Feature comparison table (Free vs Premium)
- [ ] Plan selection cards
- [ ] "Upgrade Now" CTA buttons throughout app
- [ ] Premium badge display on profile
- [ ] Premium feature gates
  - [ ] Unlimited likes
  - [ ] See who liked you (unblurred)
  - [ ] Send first message (male users)
  - [ ] No chat expiry
- [ ] Mock payment flow (UI only for MVP)
- [ ] "Successfully Subscribed" screen
- [ ] Subscription status in profile
- [ ] "Restore Purchase" option

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### ⚙️ Settings & Other (Week 4-5)
- [ ] Settings screen
- [ ] Account settings
  - [ ] Edit profile link
  - [ ] Change password
  - [ ] Phone/email verification status
- [ ] Privacy settings
  - [ ] Discovery visibility toggle
  - [ ] Show distance toggle
  - [ ] Show online status toggle
- [ ] Notification settings (UI only for MVP)
- [ ] Preference settings
  - [ ] Language selection (English/Hindi)
  - [ ] Theme (Light/Dark)
  - [ ] Distance units (km/miles)
- [ ] About section
  - [ ] App version
  - [ ] Terms & Conditions
  - [ ] Privacy Policy
  - [ ] Help & Support
  - [ ] Feedback form
- [ ] Logout option
- [ ] Delete account option (with confirmation)
- [ ] Onboarding tutorial (first-time users)
- [ ] App tour screens

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🎨 UI/UX Polish (Week 5)
- [ ] Consistent color scheme throughout
- [ ] Brand colors applied
- [ ] Typography consistency
- [ ] Button styles consistent
- [ ] Loading indicators everywhere
- [ ] Error states everywhere
- [ ] Empty states everywhere
- [ ] Smooth animations (swipe, transitions)
- [ ] Haptic feedback on actions
- [ ] Dark mode support
- [ ] Responsive layout (different screen sizes)
- [ ] Accessibility (text size, contrast)
- [ ] App icon finalized
- [ ] Splash screen with logo

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🧪 Testing (Week 6)
- [ ] Integration tests for critical flows
  - [ ] Registration flow
  - [ ] Login flow
  - [ ] Profile creation flow
  - [ ] Swipe and match flow
  - [ ] Chat flow
- [ ] Test on Android devices (5+ devices)
- [ ] Test on iOS devices (3+ devices)
- [ ] Test on different screen sizes
- [ ] Test offline behavior
- [ ] Test with poor network
- [ ] Memory leak check (profiler)
- [ ] Battery usage check
- [ ] APK size check (<50MB)
- [ ] No console errors
- [ ] All animations smooth (60 FPS)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 📦 Build & Deployment (Week 6)
- [ ] Build production APK (signed)
- [ ] Build production IPA
- [ ] Test production builds
- [ ] Google Play Store listing
  - [ ] App name
  - [ ] Short description
  - [ ] Full description
  - [ ] 8+ screenshots
  - [ ] Feature graphic
  - [ ] Icon
  - [ ] Privacy policy URL
  - [ ] Content rating questionnaire
- [ ] Apple App Store listing
  - [ ] App name
  - [ ] Subtitle
  - [ ] Description
  - [ ] Keywords
  - [ ] Screenshots (6.5", 5.5" for all screens)
  - [ ] App preview video (optional)
  - [ ] Privacy policy URL
- [ ] Submit to Google Play (internal testing)
- [ ] Submit to App Store (TestFlight)
- [ ] Internal testing (10+ users)
- [ ] Fix critical bugs
- [ ] Submit for production release

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

## ⚙️ BACKEND API (.NET Core) - CRITICAL ENDPOINTS

### 🔐 Authentication APIs (Week 1)
- [ ] POST /api/v1/auth/register
- [ ] POST /api/v1/auth/verify-otp
- [ ] POST /api/v1/auth/login
- [ ] POST /api/v1/auth/refresh-token
- [ ] POST /api/v1/auth/logout
- [ ] POST /api/v1/auth/forgot-password
- [ ] POST /api/v1/auth/reset-password
- [ ] JWT token generation
- [ ] JWT token validation
- [ ] Password hashing (BCrypt)
- [ ] OTP generation (6-digit)
- [ ] OTP storage (Redis with 5-min expiry)
- [ ] SMS sending integration (Twilio)
- [ ] Email validation
- [ ] Phone validation
- [ ] Rate limiting on auth endpoints

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 👤 Profile APIs (Week 1 & 4)
- [ ] POST /api/v1/profile (Create profile)
- [ ] GET /api/v1/profile/me (Get own profile)
- [ ] PUT /api/v1/profile (Update profile)
- [ ] POST /api/v1/profile/photos (Upload photo)
- [ ] DELETE /api/v1/profile/photos/:id (Delete photo)
- [ ] PUT /api/v1/profile/photos/reorder (Reorder photos)
- [ ] GET /api/v1/profile/:userId (Get other user's profile)
- [ ] PATCH /api/v1/profile/location (Update GPS location)
- [ ] Profile validation (age 18+, required fields)
- [ ] Photo upload to AWS S3
- [ ] Photo compression/thumbnail generation
- [ ] CDN setup for photos

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### ✔️ Verification APIs (Week 4)
- [ ] POST /api/v1/verification/submit (Submit selfie)
- [ ] GET /api/v1/verification/status (Check status)
- [ ] AWS Rekognition integration
- [ ] Face detection validation
- [ ] Manual verification queue
- [ ] GET /api/v1/admin/verifications (Get pending)
- [ ] POST /api/v1/admin/verifications/:id/approve
- [ ] POST /api/v1/admin/verifications/:id/reject
- [ ] Verification status update
- [ ] Verified badge logic

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🔍 Discovery APIs (Week 2)
- [ ] GET /api/v1/discovery/profiles (Get nearby profiles)
- [ ] POST /api/v1/discovery/swipe (Like or pass)
- [ ] GET /api/v1/discovery/who-liked-me (See who liked)
- [ ] Proximity search query (ST_Distance_Sphere)
- [ ] Spatial indexes on users table
- [ ] Filter already liked users
- [ ] Filter blocked users
- [ ] Filter users by preferences
- [ ] Prioritize verified users
- [ ] Matching score algorithm
- [ ] Caching (Redis, 10-min TTL)
- [ ] Daily like counter (Redis)
- [ ] Free tier limit enforcement (25 likes/day)
- [ ] Reset daily counter at midnight IST

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💕 Match APIs (Week 2)
- [ ] GET /api/v1/matches (Get all matches)
- [ ] GET /api/v1/matches/:matchId (Get match details)
- [ ] DELETE /api/v1/matches/:matchId (Unmatch)
- [ ] Match detection logic (mutual like)
- [ ] Match creation
- [ ] Match notification creation
- [ ] Indexes on matches table
- [ ] Pagination for matches list

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💬 Message APIs (Week 3)
- [ ] POST /api/v1/messages (Send message)
- [ ] GET /api/v1/messages/conversations (Get all conversations)
- [ ] GET /api/v1/messages/:userId (Get chat history)
- [ ] PUT /api/v1/messages/:messageId/read (Mark as read)
- [ ] DELETE /api/v1/messages/conversations/:userId (Delete chat)
- [ ] Female-first messaging logic
- [ ] Check if match exists before sending
- [ ] Message validation (max length, profanity filter)
- [ ] Pagination (50 messages per page)
- [ ] Unread message count
- [ ] Message encryption at rest
- [ ] Indexes on messages table
- [ ] Chat expiry for free users (48 hours)
- [ ] Background job for chat cleanup

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 💎 Subscription APIs (Week 5)
- [ ] GET /api/v1/subscriptions/plans (Get plans)
- [ ] GET /api/v1/subscriptions/status (Check status)
- [ ] POST /api/v1/subscriptions/mock-upgrade (Mock payment for MVP)
- [ ] Premium feature checks in all services
- [ ] Subscription status in user profile
- [ ] Premium badge logic

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🛡️ Moderation APIs (Week 4-5)
- [ ] POST /api/v1/reports (Report user)
- [ ] POST /api/v1/blocks (Block user)
- [ ] DELETE /api/v1/blocks/:userId (Unblock user)
- [ ] GET /api/v1/admin/reports (Get reports)
- [ ] PUT /api/v1/admin/reports/:id/resolve
- [ ] POST /api/v1/admin/users/:id/ban
- [ ] POST /api/v1/admin/users/:id/delete
- [ ] Content moderation (profanity filter)
- [ ] Inappropriate photo detection (AWS Rekognition)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### ⚙️ Backend Infrastructure (Week 1-6)
- [ ] Clean Architecture setup (4 layers)
- [ ] Dependency injection configured
- [ ] MySQL database connection
- [ ] Entity Framework Core setup
- [ ] Database migrations
- [ ] Redis connection
- [ ] Caching service
- [ ] AWS S3 integration
- [ ] Error handling middleware
- [ ] Request/response logging
- [ ] Rate limiting middleware
- [ ] CORS configuration
- [ ] Swagger documentation
- [ ] JWT authentication middleware
- [ ] Authorization policies

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🎯 Background Jobs (Week 3-5)
- [ ] Hangfire setup
- [ ] DailyLikeResetJob (runs at midnight IST)
- [ ] ChatCleanupJob (runs daily, deletes 48h old chats for free users)
- [ ] StoryExpiryJob (not for MVP, but setup framework)
- [ ] Job logging and error handling

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🧪 Backend Testing (Week 6)
- [ ] Unit tests for services (70%+ coverage)
  - [ ] AuthService tests
  - [ ] MatchingService tests
  - [ ] MessageService tests
- [ ] Integration tests for APIs
  - [ ] Auth endpoints
  - [ ] Profile endpoints
  - [ ] Discovery endpoints
  - [ ] Match endpoints
  - [ ] Message endpoints
- [ ] Load testing (500 concurrent users)
- [ ] Database query performance testing
- [ ] API response time testing
- [ ] Security testing (OWASP top 10)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### 🚀 Backend Deployment (Week 6)
- [ ] AWS account setup
- [ ] Production database (AWS RDS MySQL)
- [ ] Production Redis (ElastiCache or Redis Cloud)
- [ ] Production S3 bucket
- [ ] Docker image build
- [ ] Push to AWS ECR
- [ ] Deploy to AWS ECS/EC2
- [ ] Environment variables configured
- [ ] SSL certificate (Let's Encrypt)
- [ ] Domain name configured
- [ ] Health check endpoint
- [ ] Logging configured (CloudWatch)
- [ ] Monitoring (Application Insights)
- [ ] Alerting configured
- [ ] Database migrations on production
- [ ] Seed production with test data

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

## 🗄️ DATABASE - CRITICAL SETUP

### Schema Creation (Week 1)
- [ ] Users table
- [ ] Likes table
- [ ] Matches table
- [ ] Messages table
- [ ] Stories table (structure only for MVP)
- [ ] Story_replies table (structure only)
- [ ] Subscriptions table
- [ ] User_reports table
- [ ] User_blocks table
- [ ] Notifications table
- [ ] User_activity table (optional)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### Indexes (Week 2)
- [ ] Users: idx_location (latitude, longitude)
- [ ] Users: idx_verified_premium (is_verified, is_premium)
- [ ] Users: idx_last_active (last_active)
- [ ] Users: idx_gender_age (gender, age)
- [ ] Likes: unique_like (from_user_id, to_user_id)
- [ ] Likes: idx_from_user (from_user_id, liked_at)
- [ ] Likes: idx_to_user (to_user_id, liked_at)
- [ ] Matches: unique_match (user1_id, user2_id)
- [ ] Matches: idx_user1_matches (user1_id, matched_at)
- [ ] Matches: idx_user2_matches (user2_id, matched_at)
- [ ] Messages: idx_conversation (sender_id, receiver_id, sent_at)
- [ ] Messages: idx_receiver_unread (receiver_id, is_read, sent_at)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### Data Seeding (Week 2-3)
- [ ] 50+ test user profiles
- [ ] Diverse locations (Mumbai, Delhi, Bangalore, etc.)
- [ ] Mix of verified/unverified
- [ ] Mix of male/female
- [ ] Test photos uploaded
- [ ] Some pre-existing likes
- [ ] Some pre-existing matches
- [ ] Some pre-existing messages

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### Performance Optimization (Week 5)
- [ ] Analyze slow queries
- [ ] Add missing indexes
- [ ] Optimize proximity search
- [ ] Setup query caching
- [ ] Partition messages table (if needed)
- [ ] Database backup strategy

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

## 🔒 SECURITY CHECKLIST

### Must-Haves (Week 5-6)
- [ ] HTTPS enforced
- [ ] Passwords hashed (BCrypt)
- [ ] JWT tokens expire (24h)
- [ ] Refresh tokens implemented
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (input sanitization)
- [ ] CORS configured correctly
- [ ] Rate limiting on all endpoints
- [ ] File upload validation (type, size)
- [ ] No sensitive data in logs
- [ ] No API keys in code (environment variables)
- [ ] Error messages don't leak info
- [ ] Admin endpoints protected
- [ ] User data encryption at rest
- [ ] GDPR compliance (data export, delete)

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

## 📊 LAUNCH READINESS

### Pre-Launch Tests (Week 6)
- [ ] User can register and login ✅
- [ ] User can create profile ✅
- [ ] User can upload photos ✅
- [ ] User can swipe on profiles ✅
- [ ] Matches are created correctly ✅
- [ ] Users can chat ✅
- [ ] Female-first rule works ✅
- [ ] Daily like limit works ✅
- [ ] Premium upgrade flow works ✅
- [ ] App doesn't crash on critical flows ✅
- [ ] API response time < 3 seconds ✅
- [ ] App works offline (cached data) ✅
- [ ] No memory leaks ✅
- [ ] No security vulnerabilities ✅

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### Production Environment (Week 6)
- [ ] Backend deployed to production
- [ ] Database running on production
- [ ] Redis running on production
- [ ] S3 bucket configured
- [ ] Domain name pointing to API
- [ ] SSL certificate installed
- [ ] Monitoring enabled
- [ ] Alerting configured
- [ ] Backup strategy in place
- [ ] Rollback plan documented

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

### App Store Preparation (Week 6)
- [ ] Google Play Developer account created
- [ ] Apple Developer account created
- [ ] App name finalized
- [ ] App description written
- [ ] Screenshots captured (8+)
- [ ] App icon finalized (1024x1024)
- [ ] Privacy policy published (URL)
- [ ] Terms & conditions published (URL)
- [ ] Support email created
- [ ] Content rating completed
- [ ] APK signed and ready
- [ ] IPA signed and ready

**Status:** ⬜ Not Started | 🟡 In Progress | ✅ Complete | 🔴 Blocked

---

## 🎯 FINAL GO/NO-GO CHECKLIST

### ✅ GO Criteria (All must be YES)
- [ ] App launches without crashing
- [ ] User registration works
- [ ] User login works
- [ ] Profile creation works
- [ ] Photo upload works
- [ ] Discovery feed loads
- [ ] Swipe works (left & right)
- [ ] Matches are created
- [ ] Chat works (send & receive)
- [ ] Female-first rule enforced
- [ ] Daily like limit enforced
- [ ] Premium features show upgrade prompts
- [ ] No HIGH severity bugs
- [ ] API response time acceptable (<3s)
- [ ] App tested on 5+ devices
- [ ] Backend deployed to production
- [ ] Database backed up
- [ ] Monitoring active

**Total YES:** ____ / 18

**Decision:**
- 18/18 = GO! 🚀 Launch immediately
- 15-17/18 = GO with notes 📝 Fix minor issues post-launch
- <15/18 = NO-GO 🛑 Fix critical issues first

---

## 📅 LAUNCH DAY CHECKLIST (Feb 20, 2026)

### Morning (9:00 AM)
- [ ] Final smoke test on production
- [ ] Check server health
- [ ] Check database connections
- [ ] Verify all monitoring active
- [ ] Team on standby for issues

### Noon (12:00 PM)
- [ ] Submit app to Google Play (production)
- [ ] Submit app to App Store
- [ ] Post launch announcement (social media)
- [ ] Notify waitlist users (if any)
- [ ] Monitor crash reports

### Evening (6:00 PM)
- [ ] Check first user registrations
- [ ] Monitor API error rates
- [ ] Check server load
- [ ] Respond to user feedback
- [ ] Document any issues

### Night (10:00 PM)
- [ ] Final status check
- [ ] Update launch log
- [ ] Celebrate! 🎉🎉🎉

---

## 📈 POST-LAUNCH MONITORING (First 24 Hours)

### Metrics to Watch
- [ ] Total registrations: _____
- [ ] Verified users: _____
- [ ] Total swipes: _____
- [ ] Total matches: _____
- [ ] Total messages: _____
- [ ] Crash-free rate: ____%
- [ ] API error rate: ____%
- [ ] Average API response time: _____ ms
- [ ] Server CPU usage: ____%
- [ ] Server memory usage: ____%

### Issues Found
1. _________________________________
2. _________________________________
3. _________________________________

### Hotfixes Needed
1. _________________________________
2. _________________________________
3. _________________________________

---

## 🎉 CONGRATULATIONS!

**If you're reading this and all boxes are checked, you've successfully launched a dating app in 6 weeks!**

**You are AMAZING! 🚀💪🎊**

---

**Document Owner:** Bikash Kumar
**Last Updated:** January 4, 2026
**Print Date:** __________
**Printed By:** __________

---

**REMINDER:** This is your daily companion. Print it, pin it on your wall, check boxes daily!
