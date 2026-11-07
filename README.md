# IdeaBloom Mobile

> Your ideas, in full bloom.

The future mobile app for **IdeaBloom**, a personal workspace for capturing ideas and growing them into
projects, tasks, milestones, notes and resources.

## Status

📋 **Planned, not yet in development.** Mobile work starts after the web app
([ideabloom-web](https://github.com/Luciefifi/ideabloom-web)) reaches version 1.0.

This repository currently holds the plan only:

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md): purpose, planned screens, authentication flow, folder
  structure and open decisions.

## Planned tech stack

- React Native + [Expo](https://expo.dev/) (managed workflow) with Expo Router
- TypeScript
- TanStack Query for server data
- expo-secure-store for login tokens
- The existing [ideabloom-api](https://github.com/Luciefifi/ideabloom-api) REST backend. **No business
  logic is duplicated in the app.**

## Related repositories

| Repository | Role |
|---|---|
| [ideabloom-api](https://github.com/Luciefifi/ideabloom-api) | NestJS REST API: business logic, auth, database |
| [ideabloom-web](https://github.com/Luciefifi/ideabloom-web) | Next.js web app |
| ideabloom-mobile (this repo) | Future Expo app |

## Branches

| Branch | Purpose |
|---|---|
| `main` | Stable, tested code |
| `develop` | Integration branch |
| `feature/<name>` | New functionality, branched from `develop` |
| `fix/<name>` | Bug fixes |
| `release/<version>` | Release preparation |
