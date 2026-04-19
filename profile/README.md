# planno — Workforce Management Platform

> Smart scheduling, time tracking, and team communication for modern shift-based teams.

> Status: 🚧 Active Development / MVP Phase. > Core scheduling engine and database architecture are implemented. Frontend UI components are 80% complete.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Repository Structure](#repository-structure)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
  - [Mobile App (Flutter)](#mobile-app-flutter)
  - [Web App](#web-app)
  - [Backend API](#backend-api)
- [Architecture](#architecture)
- [Screens & Modules](#screens--modules)
- [API Overview](#api-overview)
- [Design System](#design-system)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

**Aplano** is a full-stack workforce management platform designed for businesses that operate on shift-based schedules — retail, hospitality, logistics, healthcare, and more. It combines scheduling, GPS-verified time tracking, absence management, and real-time team chat into a single unified experience.

The platform consists of three components:

| Component | Description |
|-----------|-------------|
| **Mobile App** | Flutter-based iOS & Android app for employees |
| **Web App** | Browser-based dashboard for managers and admins |
| **Backend API** | RESTful API powering both clients |

---


## Features

### For Employees
- **Clock In / Clock Out** with GPS-based geofencing — ensures attendance is verified at the correct location
- **My Schedule** — view upcoming shifts, weekly progress, and shift details
- **Absence Requests** — submit, track, and manage vacation, sick leave, training days, and personal days
- **Time Account** — view overtime balance, monthly trends, and request payouts or time off
- **Weekly Availability** — set recurring availability windows for scheduling
- **Team Chat** — direct messages and group channels with teammates
- **Notifications** — real-time alerts for shift changes, schedule publications, and announcements

### For Managers and Admins
- **Team Schedule** — view all staff shifts for any given day
- **Shift Management** — publish, update, and swap shifts
- **Location Override Approval** — remotely approve clock-ins from outside the geofence
- **Announcements** — broadcast messages to the whole team

---
Key Technical Highlights

Shift Collision Engine: Custom logic to prevent overlapping assignments and enforce labor-law-compliant break periods.
Server-Side Geofencing: Independent validation of GPS coordinates using the Haversine formula to ensure attendance integrity.

---
## Repository Structure

This organization hosts three separate repositories:

```
planno/
├── planno-mobile        # Flutter mobile app (iOS + Android)
├── planno-web          # Web dashboard (React / Typescript)
└── planno-backend          # Backend REST API (Node.js)
```

---

## Tech Stack

### Mobile (`planno-mobile`)
- **Framework:** Flutter (Dart)
- **State Management:** TBD (Provider / Riverpod / Bloc)
- **Location:** `geolocator` package for GPS and geofencing
- **Design:** Custom design system — `AppColors`, `AppTextStyles`, `AppDimensions`, `AppTheme`
- **Platforms:** iOS 14+ / Android 8+

### Web (`planno-web`)
- **Framework:** React + Next.js
- **Styling:** Tailwind CSS
- **Auth:** JWT / OAuth
- **Maps:** Mapbox or Google Maps

### Backend (`planno-api`)
- **Runtime:** Node.js (Express) with Prisma ORM and TypeScript.
- **Database:** PostgreSQL 
- **Auth:** JWT with refresh tokens
- **File Storage:** TBD
- **Real-time:** WebSockets (Socket.io or similar) for chat and notifications
- **Geofencing:** Server-side validation using Haversine distance formula

---

## Getting Started

### Mobile App (Flutter)

**Prerequisites**
- Flutter SDK 3.x or later
- Dart 3.x
- Xcode (for iOS builds)
- Android Studio (for Android builds)

**Setup**

```bash
# Clone the repo
git clone https://github.com/planno/planno-mobile.git
cd planno-mobile

# Install dependencies
flutter pub get

# Run on a connected device or emulator
flutter run

# Build for production
flutter build apk         # Android
flutter build ios         # iOS
```

**Required permissions** — declare in `AndroidManifest.xml` and `Info.plist`:
- `ACCESS_FINE_LOCATION` — for GPS clock-in verification
- `ACCESS_COARSE_LOCATION` — fallback location
- `INTERNET` — API communication
- `RECEIVE_BOOT_COMPLETED` — background notifications

**Environment configuration** — create a `.env` or `lib/config/env.dart` file:

```dart
const String apiBaseUrl = 'https://api.planno.com/v1';
const String mapsApiKey = 'YOUR_MAPS_API_KEY';
```

---

### Web App

```bash
git clone https://github.com/planno/planno-web.git
cd planno-web

npm install
npm run dev       # Development server at http://localhost:3000
npm run build     # Production build
npm start         # Start production server
```

---

### Backend API

```bash
git clone https://github.com/planno/planno-api.git
cd planno-api

# Install dependencies
npm install        # or: pip install -r requirements.txt

# Set up environment variables
cp .env.example .env
# Fill in: DATABASE_URL, JWT_SECRET, PORT, etc.

# Run database migrations
npm run migrate    # or equivalent

# Start development server
npm run dev        # http://localhost:8000
```

**Required environment variables:**

```env
DATABASE_URL=postgresql://user:password@localhost:5432/planno
JWT_SECRET=your_super_secret_key
JWT_REFRESH_SECRET=your_refresh_secret
PORT=8000
ALLOWED_ORIGINS=http://localhost:3000,https://app.planno.com
```

---

## Architecture

```
┌─────────────────────┐     ┌─────────────────────┐
│   Flutter Mobile    │     │      Web Dashboard   │
│   (iOS + Android)   │     │   (TypeScript/React)  │
└─────────┬───────────┘     └──────────┬───────────┘
          │                             │
          │         HTTPS / WSS         │
          └──────────────┬──────────────┘
                         │
              ┌──────────▼──────────┐
              │    planno REST API  │
              │   + WebSocket Hub   │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │     PostgreSQL DB   │
              └─────────────────────┘
```

**Key design decisions:**
- **Geofencing is enforced server-side** — the mobile app checks location as UX feedback, but the API independently validates GPS coordinates on every clock-in request
- **Clock-out is location-optional** — employees can clock out from anywhere; location is captured for records but never blocks the action
- **Real-time features** (chat, notifications) use WebSockets; all other features use standard REST

---

## Screens & Modules

### Mobile App Pages

| Screen | File | Description |
|--------|------|-------------|
| Onboarding | `onboarding.dart` | 3-step onboarding flow with feature previews |
| Login | `login_page.dart` | Email + password authentication |
| Sign Up | `signup_page.dart` | New employee account creation |
| Dashboard | `dashboard.dart` | Home screen with clock-in card, announcements, quick actions |
| Team Schedule | `teamschedule.dart` | My Schedule + Team Schedule tab view |
| My Schedule | `myschedule.dart` | Personal shift calendar with weekly progress |
| Time Clock | `clockin_page.dart` | GPS-verified clock in/out with map preview and session timer |
| Chat | `chat_page.dart` | Team direct messages and group channels |
| Notifications | `notification_page.dart` | Alerts with swipe-to-archive and urgent flagging |
| Absences | `absence_page.dart` | View and manage absence requests |
| New Absence | `new_absence_page.dart` | Submit a new absence request |
| Confirmation | `confirmation_page.dart` | Request submission confirmation |
| Request History | `request_history.dart` | Historical view of absences and shift changes |
| Time Account | `time_account_page.dart` | Overtime balance, monthly trend chart, activity details |
| Availability | `availability_page.dart` | Set weekly availability windows with time slots |
| Menu / Profile | `menu_page.dart` | Account settings, profile, and navigation to sub-pages |

### Widgets

| Widget | Description |
|--------|-------------|
| `LocationMapPreview` | Animated geofence map with pulsing radius indicator |
| `LocationCard` | Workplace address card with distance indicator |
| `TodayShiftCard` | Background-image shift card with time and location |
| `RecentActivity` | Scrollable clock-in/out event history |
| `OnDutyStatus` | Colored status badge (ON DUTY / OFF DUTY / ON BREAK) |
| `AbsenceCard` | Upcoming absence display card |
| `AbsenceCardExpandable` | Expandable past absence card |
| `AbsenceSummaryCard` | Used vs. remaining days with progress bar |
| `ActivityItem` | Single activity row with icon, title, and timestamp |

### Models

| Model | Description |
|-------|-------------|
| `WorkplaceLocation` | Workplace coordinates with geofence radius and distance calculations |
| `ShiftModel` | Shift data — role, time range, location, address, coordinates |
| `ActivityModel` | Clock event — type, time, date, icon, color |
| `Absence` | Absence record — type, date range, status, reason |
| `AbsenceSummary` | Vacation quota — used, remaining, validity |
| `WorkLocation` | Extended location model with type, timezone, manager info |

---

## API Overview

> Full API documentation will be maintained in `planno-api/docs/` or via Swagger/OpenAPI.

### Auth
```
POST   /auth/login               # Authenticate user, return JWT
POST   /auth/refresh             # Refresh access token
POST   /auth/logout              # Invalidate refresh token
```

### Time Clock
```
POST   /attendance/clock-in      # Record clock-in with GPS coordinates
POST   /attendance/clock-out     # Record clock-out
GET    /attendance/history       # Get clock history for current user
```

### Schedules
```
GET    /schedules/my             # Get authenticated user's shifts
GET    /schedules/team           # Get team schedule for a given date range
GET    /schedules/open-shifts    # Get available open shifts
POST   /schedules/swap-request   # Request a shift swap
```

### Absences
```
GET    /absences                 # Get user's absences
POST   /absences                 # Submit new absence request
PATCH  /absences/:id/cancel      # Cancel a pending absence
GET    /absences/summary         # Get vacation quota summary
```

### Chat
```
GET    /conversations            # List all conversations
GET    /conversations/:id/messages # Get messages in a conversation
POST   /conversations/:id/messages # Send a message
WS     /ws/chat                  # WebSocket for real-time messaging
```

### Notifications
```
GET    /notifications            # Get all notifications
PATCH  /notifications/read-all   # Mark all as read
PATCH  /notifications/:id/archive # Archive a notification
WS     /ws/notifications         # Real-time notification push
```

---

## Design System

All UI is built on a custom design system defined in `lib/core/theme/`:

### Colors (`app_colors.dart`)
The palette follows a semantic naming convention inspired by Tailwind CSS:

| Token | Hex | Usage |
|-------|-----|-------|
| `primary` | `#2563EB` | Buttons, links, active states |
| `slate900` | `#0F172A` | Headings, primary text |
| `slate500` | `#64748B` | Secondary text |
| `success` | `#22C55E` | Clock-in confirmation, approved status |
| `warning` | `#F59E0B` | Pending status, urgent alerts |
| `error` | `#EF4444` | Clock-out, rejected status |

### Typography (`app_text_styles.dart`)
Hierarchical text scale from `displayLarge` (40px/w900) down to `caption` (12px/w500), with semantic names: `h1`–`h6`, `bodyLarge`/`bodyMedium`/`bodySmall`, `labelLarge`/`labelMedium`, `overline`, `monospace`, and `link`.

### Spacing & Sizing (`app_dimensions.dart`)
Consistent spacing scale (`spacingXs` = 4px through `spacing6xl` = 64px), border radius (`radiusSm` = 8px through `radiusFull`), icon sizes, button heights, avatar sizes, and input dimensions.

### Theme (`app_theme.dart`)
A complete `ThemeData` configuration using Material 3, covering `ElevatedButton`, `OutlinedButton`, `TextButton`, `InputDecoration`, `Card`, `Dialog`, `BottomSheet`, `BottomNavigationBar`, `Chip`, `Switch`, `SnackBar`, `TabBar`, and more — all wired to the design tokens above.

---

## Contributing

1. Fork the relevant repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m 'feat: add your feature'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Open a Pull Request against `main`

**Commit message convention:** We use [Conventional Commits](https://www.conventionalcommits.org/):
- `feat:` — new feature
- `fix:` — bug fix
- `chore:` — maintenance / tooling
- `docs:` — documentation
- `refactor:` — code restructure without behavior change

---

## License
MIT License

Copyright © 2024 planno. All rights reserved.

---

*Built with Flutter,TypeScript and a bit of magic crafted for teams.*
