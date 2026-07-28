# UTVFarmManager

UTVFarmManager is an in-vehicle Android application that helps farm managers and field crews optimize time, stay focused, and effectively manage tasks while working on the land. The app is designed for use in UTVs and similar vehicles, providing offline-first capabilities, location-aware workflows, and simple field-focused UI to reduce distractions and increase productivity.

Table of Contents
- [Project Overview](#project-overview)
- [Architectural Decisions](#architectural-decisions)
- [High-Level Workflows](#high-level-workflows)
- [Links to READMEs & Docs](#links-to-readmes--docs)
- [Onboarding New Developers](#onboarding-new-developers)
- [Setting Up New Users / Devices](#setting-up-new-users--devices)
- [User Manual (Quickstart)](#user-manual-quickstart)
- [Contributing](#contributing)
- [Support](#support)
- [License](#license)

---
## Problem Statement
I needed a way to manage my farm without having to pay a subscription service fee to another developer. I also wanted to practice creating some products of my own where I have full control over the design, industrial design etc. so I thought this would be a good project for me to test in my unique homestead situation. A lot of the existing products out there are made for large farms and need the internet to function. The cell signal sucks at my farm so I wanted to experiment with local AI that could live in a small form factor (like a Raspberry PI).

### Hardware

## Project Overview
UTVFarmManager provides a streamlined in-vehicle experience for managing daily farm tasks, tracking work, and coordinating teams. Key goals:
- Minimize distraction and present only the most relevant information to field personnel.
- Allow offline operation with reliable data sync when connectivity is available.
- Provide simple task creation, assignment, navigation, and logging workflows suitable for UTV use.
- Integrate with backend systems for scheduling, analytics, and payroll sync.

Primary user personas:
- Farm Managers: plan and assign work, monitor progress.
- Field Operators: follow tasks and log completed work.
- Maintenance Technicians: record equipment issues and track repairs.

---

## Architectural Decisions
This section summarizes the main architecture and the reasons behind the choices. Replace specifics below with the exact technologies used in the project if they differ.

- Mobile-first, native Android app
  - Reason: Best access to device hardware (GPS, sensors), reliable background behavior, and optimized battery usage.
- Modular app structure
  - UI module (in-vehicle experience)
  - Data module (local DB + sync)
  - Services module (location, navigation, background sync)
  - Reason: Easier to test, maintain, and evolve features independently.
- Offline-first data strategy
  - Local persistent store (e.g., SQLite/Room) with a sync queue for server reconciliation.
  - Reason: Field work often has poor connectivity; the app must be reliable offline.
 
- AI Strategy
- Running offline AI on a Raspberry Pi is completely possible using lightweight open-source software like Ollama for language models, Vosk or Faster Whisper for speech-to-text, and Piper for text-to-speech. A Raspberry Pi 5 with 8GB of RAM works best for local processing without internet access
- Reason: I need an offline AI that requires no subscription cost to help plan my day's activities in an area that has bad cell reception.
- It needs to optimize timing of tasks by taking input from weather stations or syncing to my phone.
- 
- API-driven backend
  - REST/GraphQL endpoints with authentication and role-based access control.
  - Reason: Keep the mobile surface area simple and delegate heavy logic/analytics to the server.
- Security & privacy
  - Encrypted local storage for sensitive data, token-based auth, least-privilege access, and secure backups.
  - Reason: Field and personnel data must be protected.
- Telemetry & crash reporting
  - Integrate analytics and crash reporting to prioritize improvements based on real usage.
- Testing & CI
  - Automated unit tests, instrumentation tests, and a CI pipeline for PR checks and release builds.

(If you have chosen specific frameworks like Jetpack Compose, Kotlin Coroutines, Retrofit, Room, or a particular backend stack, list them below to be concrete.)

---

## High-Level Workflows
A concise overview of typical user flows. Detailed use cases are in use_cases.md — see ./use_cases.md.

- Start shift
  - Operator signs in on the device (or device auto-auths), optionally selects active vehicle.
  - App fetches assignments and localizes tasks for the day.
- Accept & execute task
  - Operator opens a task, reviews instructions, navigates to field location, marks work start/stop.
  - Photos, notes, and time logs can be attached.
- Create ad hoc task
  - Field operator can create a task (subject to manager approval) for issues discovered in the field.
- Sync & reconciliation
  - App queues changes while offline and syncs with backend when connection is available.
  - Conflict resolution follows a last-writer-wins or manager-reviewed policy (document exact policy here).
- Admin workflows
  - Managers create users, assign roles, configure geofenced areas and task templates.
  - Managers review logs, approve timesheets, and push updates to devices.

For full scenarios and edge cases, please consult ./use_cases.md.

---

## Links to READMEs & Docs
- This README: ./README.md
- Use cases (detailed flows and examples): ./use_cases.md
- (If present) App module README: ./app/README.md
- (If present) Backend README: ./backend/README.md
- (If present) /docs/ - additional architecture and deployment docs: ./docs/README.md

Add any other module-specific README files and docs here. If a file doesn't exist, create it in the appropriate directory.

---

## Onboarding New Developers
A step-by-step guide to get new developers up and running.

Prerequisites
- Install Android Studio (recommended version: document exact version)
- JDK matching the build configuration (e.g., Java 11)
- Node/npm or other tooling if parts of the project use JavaScript/TypeScript
- Access to repository and any required API keys or secrets (see credentials below)

Local setup
1. Fork and clone the repository:
   - git clone git@github.com:kldesign1-spec/UTVFarmManager.git
2. Open with Android Studio and let it sync Gradle/Dependencies.
3. Configure local environment secrets:
   - Create a local config file (e.g., ./local.properties or .env) — DO NOT commit secrets.
   - Document required keys: API_BASE_URL, AUTH_CLIENT_ID, MAPS_API_KEY, etc.
4. Build and run on emulator or physical device:
   - ./gradlew assembleDebug
   - Or use Android Studio run configurations.
5. Run tests:
   - ./gradlew test
   - ./gradlew connectedAndroidTest (for instrumentation tests)

Branching & workflow
- Feature branches: feature/<short-description>
- Bugfix branches: fix/<short-description>
- Pull requests: open a PR to main (or dev) with a clear description, link to use cases, and list of changes.
- Code review: follow code style rules, run linters, and include tests for new logic.

Developer guidelines
- Follow project lint rules and commit message conventions.
- Keep UI changes simple and optimized for in-vehicle use (large touch targets, minimal prompts).
- Document major changes and update use_cases.md if behavior changes.

---

## Setting Up New Users / Devices
A high-level process for provisioning users and devices:

1. Create user account (Manager role)
   - Via Admin UI or backend API: register user, assign role (manager/operator).
2. Invite field users
   - Manager sends invite links or provision codes which are entered on the device.
3. Device provisioning
   - Pair device to user with provisioning code or QR.
   - Device registers with the backend and receives latest config and task queue.
4. Role & permission assignment
   - Managers configure available actions and geofenced areas for operators.
5. Training & first-run
   - Provide in-app quickstart tour and offline training materials.
6. Recovery & replacement
   - If device lost, revoke device token, re-provision new device and restore the user’s pending tasks.

Detail exact admin screens, API endpoints, and security checks here based on your backend.

---

## User Manual (Quickstart)
Quick steps for field operators:

Getting started
- Turn on device, sign in (or use device provisioning).
- From the home screen, see today’s tasks and active tasks.
- Tap a task to view details: location, instructions, estimated time.
- Press Start when you begin work and Stop when finished.

Logging details
- Attach photos or notes to a task before or after completion.
- If connection is lost, data is saved locally and will sync automatically.

Navigation
- Tap Navigate to open the preferred navigation provider to reach the field.

Troubleshooting
- If a task doesn’t sync, check network status and force sync in Settings > Sync.
- For permission issues, contact your manager to confirm role assignment.

For a complete user manual and screenshots, add a dedicated USER_MANUAL.md or a /docs/user_manual.md and link it here.

---

## Contributing
We welcome contributions. Typical flow:
1. Open an issue describing the change or bug.
2. Create a fork and a feature branch.
3. Add tests and update documentation.
4. Open a Pull Request with a clear description and reference to related issues.

See CONTRIBUTING.md for more detailed rules (create this file if not present).

---

## Support
- Report issues using the repository Issues tab.
- For urgent operational problems, contact: (add contact email or slack)
- For product feedback, create an issue tagged `enhancement` or `feedback`.

---

## License
Specify your project license here (for example MIT, Apache 2.0). Add LICENSE file to the repo.
