# IdeaBloom Mobile: Architecture Plan

> **Status: plan only.** No code exists yet. Mobile development starts after the web app is
> sufficiently complete (after web v1.0). This document records the intended design so the backend can
> be built mobile-ready today.

## 1. Purpose

A companion app for the moments when you're away from your computer:

- **Capture ideas instantly**, the most important mobile feature
- See **today's and overdue tasks**, and tick them off
- Check **project progress** and upcoming deadlines
- Read and jot down **notes**; save a **link** you found on your phone

It is not meant to replicate every web feature. Heavy editing (project planning, organising categories,
long documents) stays on the web.

## 2. How it fits in

```
 ideabloom-web (Next.js) ──/api/* rewrite──┐
                                           ▼
                                  ideabloom-api (NestJS) ──► PostgreSQL
                                           ▲
 ideabloom-mobile (Expo) ── HTTPS + Bearer ┘
```

- The mobile app calls the **same REST API** as the web app (`/api/v1/...`).
- **No business logic is duplicated.** Progress calculation, overdue rules and permissions all stay in
  the API. The app only displays and submits data.
- The API's error format (`statusCode`, `error`, `message`, `path`, `timestamp`) is shared, so error
  handling works the same way as on the web.

## 3. Planned technology

Versions are checked and decided when development starts. Expo releases move quickly.

| Concern | Planned choice | Why |
|---|---|---|
| Framework | React Native + **Expo** (managed workflow) | One TypeScript codebase for Android and iOS; no native toolchains needed to start |
| Language | TypeScript | Same as web and API |
| Navigation | **Expo Router** | File-based routing, the same mental model as the Next.js App Router |
| Server data | **TanStack Query** | Same caching and loading/error patterns as the web app |
| Forms | react-hook-form + zod | Same as the web app |
| Token storage | **expo-secure-store** | Uses the phone's encrypted keychain/keystore, never plain storage |
| Styling | Lavender design tokens (same palette and contrast rules as `ideabloom-web`) | Consistent brand |
| Tests | Jest + React Native Testing Library | Standard for Expo |

At the time of writing (November 2026), the current Expo SDK is **58**.

## 4. Authentication (how it differs from the web)

| | Web | Mobile |
|---|---|---|
| How the token is sent | httpOnly **cookie** (browser handles it) | `Authorization: Bearer <access token>` header |
| Where tokens are stored | Browser cookie jar | **expo-secure-store** |
| Refresh | Cookie sent to the refresh endpoint | Refresh token sent in the request body |
| CORS | Not needed (same-origin rewrite) | Not needed (native apps aren't subject to CORS) |

Flow:

1. Log in → the API returns `{ accessToken, refreshToken }` in the response body for mobile clients.
2. Every request sends the access token (short-lived, about 15 minutes).
3. On `401`, the app calls the refresh endpoint once, stores the new pair (refresh tokens rotate), and
   retries the request.
4. If refresh fails, the app clears the tokens and returns to the login screen.
5. Logout calls the API so the refresh token is revoked server-side.

**Backend requirements** (to build in Milestone 1 so mobile needs no API changes later):

- Auth guard accepts the token from a cookie **or** the `Authorization` header.
- Login and refresh can return tokens in the body for non-browser clients.
- Refresh tokens are stored hashed and rotated, which allows "log out of all devices".

## 5. Planned screens (first mobile version)

| Screen | Purpose |
|---|---|
| Login / Register / Forgot password | Account access |
| Home | Today's tasks, overdue count, upcoming deadlines, recent ideas |
| **Quick capture** | One-tap idea entry (title only, everything else optional) |
| Ideas | List, search, filter by status, favourite, view details |
| Tasks | Pending / overdue / completed; complete and reopen |
| Projects | List with progress, project detail with milestones (mostly read-only) |
| Notes | Read, create short notes |
| Resources | Save a link (with the phone's share sheet in a later version) |
| Settings | Profile, logout, log out all devices |

Later ideas (not in the first version): offline capture queue, push notifications for deadlines, share
sheet integration, home-screen widget.

## 6. Planned folder structure

```
app/                     Expo Router routes (like Next.js app/)
  (auth)/                login, register, forgot-password
  (tabs)/                home, ideas, tasks, projects, more
src/
  lib/api-client.ts      fetch wrapper: base URL, Bearer token, refresh-on-401, ApiError
  lib/secure-storage.ts  token read/write via expo-secure-store
  features/<feature>/    API hooks and components per feature (same idea as web)
  components/            shared UI
  theme/                 lavender tokens
```

## 7. Local development notes (for later)

- A phone **cannot reach `localhost`** on your computer. Use your computer's local network IP (for
  example `http://192.168.1.20:4000`) through an `EXPO_PUBLIC_API_URL` setting, with phone and computer
  on the same Wi-Fi. The Android emulator uses `http://10.0.2.2:4000`.
- Only the API needs to be reachable from the phone. The databases stay bound to `127.0.0.1`.
- Windows firewall may need to allow Node on port 4000 for the private network.

## 8. Open decisions (to make when mobile work starts)

- Exact Expo SDK version and the Node version it needs (the machine may need Node 24 by then).
- Styling approach for React Native (plain `StyleSheet` with tokens vs a Tailwind-style library).
- Distribution: Expo Go for personal testing, then **EAS Build** for installable apps. EAS has free
  and paid tiers, and publishing to app stores costs money (Apple developer fee). **The owner decides
  before anything paid is used.**
- Whether to share TypeScript types with the API (for example a generated API client from Swagger/OpenAPI).
