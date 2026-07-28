# Welcome Dashboard — Recommended Components

This file lists the recommended components for the app welcome/dashboard screen when a user starts their workday on the farm. Items are prioritized for a small family-run farm: action-focused, minimal clutter, glove-friendly, and offline-capable.

## Primary (visible on welcome screen)

- Driving Quick Task List (max 4 tasks)
  - Each row: title, short field name, distance, ETA (optional), single large primary action (Start / Complete)
  - Large font default (>= 20pt) and large tappable targets for glove use
  - Sorted by proximity and priority
  - Reference: UTV-001, UTV-016

- Start-of-shift card
  - Start / Stop shift with simple hours timer and optional quick note
  - Tapping Start logs timestamp and gps
  - Reference: UTV-015

- Prominent Weather & Safety Banner
  - Current conditions summary, At-Risk / Heat advisory flag, tap for details
  - Minimal recommendations (e.g., "Suggest morning window") rather than full forecasting UI
  - Reference: UTV-004, UTV-005

- Critical Alerts strip
  - One-line carousel for incidents (panic, thunderstorm warnings), with one-tap Acknowledge / View
  - Panic button is separate and prominent (see Quick Actions)
  - Reference: UTV-009

- Mini live map snapshot
  - Small map showing current field polygon and nearby crew/equipment icons
  - One-tap to open full map or navigation
  - Reference: UTV-006, UTV-011

- Big Quick Actions row
  - Quick Add Task, Quick Photo + GPS, Navigate to next task, Panic button (with cancel window)
  - Actions use large buttons and short labels/icons for glove use
  - Reference: UTV-002, UTV-007, UTV-009, UTV-011

- Sync & Status indicators
  - Network state (Online / Offline), pending sync count, GPS accuracy, battery
  - Tapping shows sync queue and recent pending items
  - Reference: UTV-018

- Nearby crew / presence summary
  - Names and distance (or "Location hidden") for opt-outs; one-tap Ping
  - Configurable radius, default ~5 km
  - Reference: UTV-014, UTV-017

- Today’s recommended items / auto-reschedule suggestions
  - Short list (1–2) of high-priority recommendations or auto-reschedules with Accept / Dismiss
  - Small footprint; expand for details
  - Reference: UTV-005, UTV-004

- Accessibility / Driving Mode indicator
  - Show Driving Mode state and the reduced-interaction banner when engaged (automatically detects speed >5 mph)
  - Font size and contrast quick access
  - Reference: UTV-003, UTV-016

- Simple equipment status peek
  - One-line status for 1–2 named pieces of equipment (e.g., Tractor A — On task / Idle / Fault)
  - Tap to view equipment checklist or maintenance task
  - Reference: UTV-006, UTV-010

- Short recent activity feed (last 3 items)
  - Last 3 completions or incidents with timestamps and quick link to task or photo
  - End-of-day report shortcut
  - Reference: UTV-015

## Secondary (hidden behind swipe or “More”)

- Full task list and route optimization (opt-in)
- Coverage % and detailed pass-tracking maps
- Detailed equipment telemetry and checklists
- Conflict-resolution UI for offline edits
- Reports & PDF export

## UI behavior / defaults (implementation notes)

- Layout: Top bar (date/time + Start Shift), primary column (4 large tasks), slim weather banner, quick actions, map thumbnail, status bar.
- Driving Mode: auto-enable when device speed >5 mph for >5s; restrict interactions to primary actions and quick photo; show prominent banner.
- Offline: show Pending Sync badges; Quick Add, Photo, Checklists should work offline and queue sync.
- Privacy: clearly show "Location hidden" for opt-outs and an easy per-session toggle.

## Prioritization suggestion for a small-team MVP

1. Driving Quick Task List (4 tasks) + Big Quick Actions + Start-of-shift card (P0)
2. Driving Mode + Accessibility defaults (P0)
3. Mini map snapshot + Nearby crew (P0/P1)
4. Weather & Safety banner + Critical Alerts (P0/P1)
5. Offline sync indicators + Pending Sync workflow (P0)
6. Auto-reschedule suggestions (P1)

---

Generated from USE_CASES.md (UTV-001..UTV-018) and tailored for small family-run farm needs. Keep the welcome screen minimal and action-focused; advanced tools live behind one-tap expansions.
