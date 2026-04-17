# Project Master Plan: Partner Period & Care Tracker

## 1. Project Overview & Philosophy
**App Purpose:** A companion app that connects couples by allowing the female partner to share her menstrual cycle data with her partner. The app translates complex hormonal phases into digestible, actionable care tips for the boyfriend/partner, eliminating guesswork and fostering empathy.

**Core Principles:**
- **Consent-First:** Data sharing is strictly opt-in and easily revocable by the female partner.
- **Anti-Monolith:** Customizable symptoms and tips (e.g., acknowledging that not everyone gets angry during PMS).
- **Educational:** Grounded in biological facts (explaining why symptoms happen based on the 4 cycle phases).

## 2. Technology Stack Definition
- **Frontend Mobile:** React Native (Expo recommended for rapid iteration), TypeScript, React Navigation, Zustand (for state management), React Query (for API data fetching).
- **Backend API:** ASP.NET Core 8/9 Web API, Entity Framework (EF) Core.
- **Backend Orchestration:** .NET Aspire (handles local dev orchestration, wires up the PostgreSQL container, and manages observability/telemetry).
- **Database:** PostgreSQL (relational structure is perfect for user pairings and cycle logs).
- **Push Notifications:** Firebase Cloud Messaging (FCM) or Apple Push Notification service (APNs).

## 3. Database Architecture (PostgreSQL / EF Core Models)
Core entity relationships:
- **Users:** UserId, Email, PasswordHash, Role (Tracker vs. Partner), PairedUserId (Nullable foreign key linking the couple).
- **CycleLogs:** LogId, UserId (The Tracker), StartDate, ExpectedEndDate, CycleLength, Phase (Enum: Menstrual, Follicular, Ovulation, Luteal).
- **DailySymptoms:** SymptomId, CycleLogId, Date, MoodLevel, EnergyLevel, PhysicalSymptoms (Array/JSON).
- **CareProfiles (The "Cheat Sheet"):** ProfileId, UserId, PhaseEnum, DoTips (List of strings, e.g., "Bring heating pad"), DontTips (List of strings, e.g., "Ask what's for dinner"), FavoriteSnacks.
- **EducationalContent:** ContentId, PhaseEnum, Title, BiologicalExplanation, PartnerActionableAdvice.

## 4. Backend Development Plan (.NET Core + Aspire)
Scaffold the backend using the .NET Aspire Starter Application template.

### A. The Aspire AppHost
- Configure the `.AppHost` project to spin up a PostgreSQL container locally.
- Inject the PostgreSQL connection string into the `.ApiService` project seamlessly using Aspire's `AddNpgsqlDbContext` integration.
- Ensure OpenTelemetry (built into Aspire Service Defaults) is logging all API requests for easy debugging.

### B. Core API Endpoints (RESTful)
- **Auth & Pairing:**
  - `POST /api/auth/register` & `/api/auth/login` (JWT implementation).
  - `POST /api/pair/generate-code` (Creates a 6-digit expiring code for the female partner).
  - `POST /api/pair/link` (Boyfriend enters code to link accounts).
- **Cycle Management:**
  - `POST /api/cycle/log` (Female partner logs period start/end).
  - `GET /api/cycle/current` (Calculates and returns current phase, day of cycle, and countdowns).
- **Partner Dashboard:**
  - `GET /api/partner/dashboard` (Returns aggregated data: current phase, biological explanation, and tailored CareProfile tips for today).
- **Customization:**
  - `PUT /api/careprofile/update` (Female partner updates her specific needs for a phase).

## 5. Frontend Architecture (React Native + TypeScript)
Structure the Expo/React Native app with distinct flows based on the user's role.

### A. Navigation Flow
- **Auth Stack:** Login, Signup, Role Selection (Am I tracking my cycle, or am I supporting my partner?).
- **Pairing Screen:** Beautiful UI for sharing/entering the 6-digit sync code.
- **Main App (Partner View):**
  - *Home Tab:* A dynamic, visually distinct dashboard that changes colors based on the phase. Large central indicator ("She is on Day 22: Luteal Phase"). Immediate "Care Tips for Today".
  - *Learn Tab:* The educational hub explaining the biology of the 4 phases.
  - *Cheat Sheet Tab:* Direct access to her customized list of favorite snacks, comfort items, and boundaries.
- **Main App (Tracker View):**
  - *Log Tab:* Clean calendar UI to log period dates and daily symptoms.
  - *Customize Tab:* Forms to edit her CareProfile (e.g., "Update my Luteal Phase needs").

### B. UI/UX Guidelines
- Use a UI library like Tamagui or React Native Paper for fast, accessible, and clean cross-platform components.
- Implement a visual ring or progress bar component (using `react-native-svg`) to visually represent the 28-ish day cycle on the Partner's home screen.

## 6. Implementation Phases
- **Phase 1: Infrastructure & Auth.** Setup the Aspire AppHost, PostgreSQL container, and React Native shell. Build the JWT authentication, User roles, and the 6-digit pairing logic.
- **Phase 2: The Cycle Engine.** Build the EF Core models for CycleLogs. Create the backend algorithm that estimates the 4 phases based on a provided start date and average cycle length.
- **Phase 3: The Partner Dashboard.** Build the API that serves the customized "Cheat Sheet" and biological data based on the current cycle day. Build the React Native UI to display this cleanly.
- **Phase 4: Notifications.** Integrate Push Notifications (e.g., "Incoming: Luteal Phase starts in 2 days. Check your cheat sheet for prep tips!").

## 7. Crucial Security Rules
- **Endpoint Authorization:** Ensure the Partner's API endpoints verify that `User.PairedUserId` matches the requested cycle data. A user should never be able to query a cycle ID that doesn't belong to their paired partner.
- **Instant Revocation:** The API must support an endpoint where the female partner can "Disconnect Partner," immediately nullifying the relationship in the database and logging the partner out of the dashboard.
