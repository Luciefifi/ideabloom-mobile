# IdeaBloom Mobile: Claude Code Guide

This repo is the **future mobile app** of IdeaBloom (React Native + Expo + TypeScript).

**Status: planning only.** Do not scaffold Expo or implement features until the owner says the web app is
sufficiently complete. For now this repo holds a README and an architecture plan.

**Full project context:** the shared guide at `../CLAUDE.md` (decisions, milestones, progress log) and
the brief at `../docs/PROJECT_BRIEF.md`. Claude Code loads `../CLAUDE.md` automatically when started
inside this folder.

## Rules
- **Never** `git commit`, `git push`, force-push or pull with rebase. **Never** add Claude attribution or
  Co-authored-by lines. The owner commits and pushes everything.
- Never work on `main`. Use `feature/*` or `fix/*` branches from `develop`.
- The app will call the same `ideabloom-api` endpoints, using `Authorization: Bearer` tokens kept in
  secure device storage. No business logic is duplicated here.
