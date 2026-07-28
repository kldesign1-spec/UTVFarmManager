# UTV Farm Manager — Use Cases & Backlog

This file contains backlog-ready user stories and acceptance tests for the UTV-focused Farm & Land Maintenance Task Manager. Format per story:
- ID / Epic
- Title
- As a <role>, I want <goal> so that <benefit>
- Acceptance Criteria (numbered, testable)
- Test Cases (steps + expected result)
- Priority (P0/P1/P2) and Story Points

---

## UTV-001 — Quick Task List (MVP)
Title: Quick Task List (4-item, large-font driving view)
As a farmer in my UTV, I want to see up to 4 active tasks with large, readable UI so that I can safely view and complete tasks while driving.

Acceptance Criteria:
1. The home driving view displays a maximum of 4 active tasks sorted by proximity and priority.
2. Each task row includes: title, short field name, distance (mi/km), ETA (optional), and a single large primary action (Start / Complete).
3. Default font size for task title >= 20pt (configurable in Settings).
4. Tapping Complete logs a timestamp and the current GPS coordinates.
5. If there are more than 4 active tasks, a visible pagination or "More" button navigates to full task list.

Test Cases:
1. Given 6 active tasks, when opening the driving view, then only 4 tasks are rendered in the primary list.
2. Given a task visible in the list, when the user taps Complete, then the app records completion time and GPS and removes or archives the task from the active list.
3. Given low-light or sunlight conditions, when switching to sunlight mode, then the driving view uses high-contrast colors and text remains readable.

Priority: P0 — Points: 3

---

## UTV-002 — Create / Edit Task from UTV (Quick Add)
Title: Quick Add Task
As a UTV operator, I want to create or edit a task with minimal input so that I can log work without lengthy typing.

Acceptance Criteria:
1. Quick Add requires at most 3 taps to create a task: Title, Field (auto-suggest by proximity), Priority.
2. Voice input is available for Title and short notes; falls back to keyboard when desired.
3. If offline, the new task saves locally and marks as "Pending Sync" with visible status.

Test Cases:
1. With network off, create a task via Quick Add; app stores the task locally and shows Pending Sync. After network returns, the task syncs automatically.
2. Use voice input to create a task; the spoken text populates the Title field accurately (reasonable error tolerance).

Priority: P0 — Points: 2

---

## UTV-003 — Driving Mode / Hands-free Mode
Title: Driving Mode
As a driver, I want the app to limit complex interactions and allow voice commands when the vehicle is moving so that I remain focused and safe.

Acceptance Criteria:
1. Driving Mode automatically engages when device speed > configurable threshold (default 5 mph / 8 kph) for >5 seconds.
2. In Driving Mode, only these are allowed: view 4-task list, Start/Complete task, Quick Photo (single button), and one-tap Panic/Alert.
3. Complex forms, long text entry, and navigation to settings are disabled until Driving Mode is deactivated.
4. Driving Mode displays a prominent banner: "Driving Mode Active — limited interactions".

Test Cases:
1. Simulate device moving >5 mph; app switches to Driving Mode and hides complex buttons. When stopped, app exits Driving Mode after 3 seconds and restores full UI.
2. Attempt to edit a task detail while Driving Mode active; the app blocks the action and shows a message.

Priority: P0 — Points: 3

---

## UTV-004 — Weather-Aware Task Recommendation
Title: Weather-aware scheduling
As a farm manager, I want the app to recommend time windows for tasks based on weather so that crews avoid heat exposure and adverse weather.

Acceptance Criteria:
1. The system fetches local field forecasts and computes a recommended time window for each open task considering temperature, precipitation, wind, and soil moisture where available.
2. For tasks with worker heat exposure risk, the app flags tasks with a heat advisory when heat-index exceeds configurable threshold (e.g., 95°F / 35°C) and suggests an earlier/later time window.
3. Recommendations appear as a small banner on each task and in a daily recommendations list.

Test Cases:
1. Given forecasted heat index > threshold for afternoon, when viewing a task with heat exposure, then banner suggests morning window (e.g., 06:30–09:00) with reason text.
2. If precipitation > 90% during assigned time, the app suggests rescheduling and marks the task as "At Risk".

Priority: P1 — Points: 5

---

## UTV-005 — Auto-Reschedule & Notifications
Title: Auto-reschedule hazardous tasks
As a manager, I want the app to auto-suggest rescheduling for tasks when weather or safety conditions become hazardous so that crew safety and efficiency improve.

Acceptance Criteria:
1. The app can auto-suggest reschedule times and notify assigned crew via in-app push or SMS when a task is flagged as hazardous.
2. Auto-reschedule actions must create an audit log entry with reason and timestamp.
3. Users can accept or override auto-reschedule; overrides are recorded.

Test Cases:
1. When a thunderstorm warning is issued for a field with an upcoming open-field task, then the app creates a suggested reschedule and sends notifications to assigned users.
2. When a user overrides the suggestion, then the override is saved with user ID and reason.

Priority: P1 — Points: 3

---

## UTV-006 — Live Equipment/Crew Progress Map
Title: Live progress visualization
As a farm owner, I want to see live equipment/crew positions and job progress on a map so that I can monitor ongoing work (e.g., tractor seeding a field).

Acceptance Criteria:
1. Equipment/crew locations update at a configurable interval (e.g., every 5s when active, every 60s idle) while on a job.
2. For coverage tasks (seeding, spraying), the app computes and displays percent-complete based on GPS track + implement width (configurable) or area covered algorithm.
3. Map view shows a simple color overlay of coverage and a percent label; tapping a unit shows current task, speed, and ETA.

Test Cases:
1. Start a seeding task on Tractor A; as GPS tracks progress, the coverage overlay grows and percent-complete updates toward 100%.
2. With implement width unknown, the app falls back to path-length heuristic and shows a confidence score "Low/Medium/High".

Priority: P0 — Points: 8

---

## UTV-007 — Quick Photo + GPS Note (Scouting)
Title: Quick photo & note capture
As a driver, I want to capture a photo with a short note and GPS tag so that I can log scouting observations quickly.

Acceptance Criteria:
1. Capture flow: tap Camera → take photo → select reason from dropdown (pest/disease/fence/other) → optional 10s voice note → Save.
2. The saved record contains GPS coordinates, timestamp, field association, and photo thumbnail.
3. Works offline and syncs later; record status shows Pending Sync.

Test Cases:
1. Take photo in no-network area; the app stores record locally and flags Pending Sync. After connecting, the record syncs to server.
2. Create a photo note and verify the GPS coordinate is within field bounds.

Priority: P0 — Points: 2

---

## UTV-008 — Proof of Work (Geo-verified Completion)
Title: Geo-verified task completion
As a manager, I want multi-step or critical tasks to require photo + geofence proof to mark complete so that I can have evidence of work done.

Acceptance Criteria:
1. For tasks marked as "Require Proof", the Complete button opens a capture modal requiring one photo and auto-attached GPS within the task geofence.
2. If the photo GPS is outside geofence by > preset tolerance, app warns user and requires override with reason.
3. Completed proof is stored in task history with timestamp and uploader.

Test Cases:
1. Complete a fence repair task within geofence: app accepts photo and marks complete.
2. Attempt to complete the same task from >200m away: app warns and prevents completion unless user provides override reason.

Priority: P1 — Points: 3

---

## UTV-009 — Panic / Equipment Fault Alert
Title: Panic / Fault alert
As a driver, I want to send an urgent alert with my location and recent GPS trace so that help can be dispatched quickly.

Acceptance Criteria:
1. Panic button sends an immediate high-priority notification to designated contacts (in-app + SMS/email) with last-known location and last 5 min GPS trace.
2. The alert creates an incident record in system with timestamp, sender ID, and resolution status.
3. User can cancel the alert within 30 seconds to prevent false alarms.

Test Cases:
1. Tap Panic; designated contacts receive alert message with coordinates and link to live location.
2. After false press, user cancels within 20s; notifications are retracted or marked canceled where supported.

Priority: P0 — Points: 2

---

## UTV-010 — Maintenance Checklists
Title: Equipment Pre/Post-use Checklist
As an operator, I want to run quick maintenance checklists from my UTV so that routine maintenance is logged and faults are caught.

Acceptance Criteria:
1. Checklists are templated per equipment type; each checklist item is a large checkbox and optional photo evidence.
2. Completing checklist logs operator, time, equipment, and any photos; if a critical item fails, the system can auto-create a maintenance task.
3. Checklists can be executed offline and sync later.

Test Cases:
1. Run pre-use checklist, mark a critical fault; the system auto-creates a maintenance task assigned to mechanics.
2. Complete checklist offline; upon network return, checklist syncs and appears in equipment history.

Priority: P1 — Points: 3

---

## UTV-011 — Navigation to Task / Field
Title: One-tap navigation
As a driver, I want one-tap navigation to task start point or field entry so that I can reach work locations faster.

Acceptance Criteria:
1. Each task includes a "Navigate" button; tapping opens an in-app map with simple turn guidance or exports to installed navigation app.
2. Offline map tiles are cached for mapped fields; fallback to coordinates if no map tiles.

Test Cases:
1. Tap Navigate to Field B; map opens and shows route and distance.
2. With no network, Map uses cached tiles; if no tiles, shows coordinates and distance.

Priority: P0 — Points: 3

---

## UTV-012 — Route Optimization for Multiple Tasks
Title: Route optimization
As an operator, I want the app to suggest an optimized order for multiple tasks to minimize travel and respect weather windows so that I save time and fuel.

Acceptance Criteria:
1. Given a set of tasks with locations and priorities, the app computes an optimized route considering distance and recommended weather windows.
2. The optimization can be toggled to prefer either "Shortest distance" or "Respect weather".

Test Cases:
1. Create 6 tasks across the farm; request optimize route by distance; system returns an ordered list with total distance estimate.
2. Same tasks with respect-weather option prioritizes tasks in good windows.

Priority: P2 — Points: 5

---

## UTV-013 — Coverage % and ETA for Pass-based Tasks
Title: Coverage & ETA tracking for implements
As a farm manager, I want the app to estimate percent-complete for pass-based tasks (seeding, spraying) so that I know when to reassign equipment.

Acceptance Criteria:
1. Coverage % computed from GPS track overlapped with field polygon and implement width (or fallback heuristic) and updates live.
2. ETA derived from recent instantaneous rate (ha/hr) and remaining area.
3. System shows a confidence indicator when data is incomplete.

Test Cases:
1. Run seeding pass; coverage % increases and ETA updates reasonably as work proceeds.
2. If GPS samples are sparse, app shows "Low confidence" and suggests increasing sample frequency.

Priority: P0 — Points: 8

---

## UTV-014 — Crew Presence & Proximity
Title: Nearby crew and ping
As a driver, I want to see crew members near me and ping them for help so I can coordinate tasks efficiently.

Acceptance Criteria:
1. A "Nearby" filter lists crew within configurable radius (default 5 km) and their current task status.
2. One-tap ping sends a short message or audible ping to selected crew.
3. Crew can opt out of location sharing; opt-outs show "Location hidden".

Test Cases:
1. Crew member within 1 km appears in Nearby list; tap Ping and verify they receive a message.
2. Opt-out user does not appear in the Nearby listing and displays appropriate indicator.

Priority: P1 — Points: 3

---

## UTV-015 — End-of-shift Auto Report (Summary)
Title: End-of-day shift summary
As an owner, I want a short summary of completed tasks, hours, and issues at shift end so that I can review daily progress.

Acceptance Criteria:
1. System compiles a PDF/short summary with completed tasks, total time, equipment usage, and flagged issues.
2. Summary is available in-app and can be emailed or exported.

Test Cases:
1. End-of-day run: generate summary and verify counts match completed task records.
2. Export summary to email and verify email content and attachment.

Priority: P2 — Points: 5

---

## UTV-016 — Accessibility & Glove-friendly UI
Title: Glove-friendly UI
As a driver wearing gloves, I want large tappable targets and adjustable fonts so I can interact with the app while driving.

Acceptance Criteria:
1. Default tappable targets meet minimum 9–12 mm physical size on common screen densities.
2. Settings contains Font Size and High-Contrast Mode persisted per device.
3. Driving view layout and buttons tested for glove interaction.

Test Cases:
1. Increase Font Size in settings and verify task titles grow and remain within layout.
2. Switch to High-Contrast Mode and verify color contrast passes WCAG AA for text.

Priority: P0 — Points: 2

---

## UTV-017 — Privacy & Location Controls
Title: Crew privacy and tracking consent
As a crew member, I want to control when my location is shared so that my privacy is protected.

Acceptance Criteria:
1. Each user can toggle location sharing per session with a clear status indicator.
2. Admins can see sharing status but cannot force-enable sharing without consent.
3. Location sharing defaults to off for new users until explicitly enabled.

Test Cases:
1. New user signs up; location sharing is off. User enables it; app starts sharing on start of task.
2. User toggles sharing off; Nearby lists and maps reflect the new state.

Priority: P1 — Points: 2

---

## UTV-018 — Offline Sync & Conflict Handling
Title: Offline-first sync and conflict resolution
As a UTV user in remote areas, I want the app to work fully offline and sync reliably later, with clear conflict resolution for edits.

Acceptance Criteria:
1. App stores tasks, photos, checklists locally when offline and displays Pending Sync status.
2. On sync, conflicts (simultaneous edits) are flagged and presented to the user with options: Keep Local / Keep Remote / Merge (where applicable).
3. Sync status is visible for each record (Synced / Pending / Conflict).

Test Cases:
1. Edit a task offline; another user edits same task online. On reconnect, conflict appears with both versions and the local user is prompted to choose.
2. Upload multiple pending photo notes once network restored; all pending items sync successfully.

Priority: P0 — Points: 8

---

# Notes
- IDs follow UTV-### pattern and can be mapped to sprints.
- Story points are estimates and should be re-estimated by the dev team.
- Several P0 items (Quick Task List, Coverage tracking, driving mode) are recommended for MVP.

---

End of file.
